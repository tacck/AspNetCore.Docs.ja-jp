---
title: ASP.NET Core での依存関係の挿入
author: rick-anderson
description: ASP.NET Core で依存関係の挿入を実装する方法とそれを使う方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003178"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="621db-103">ASP.NET Core での依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="621db-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="621db-104">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="621db-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="621db-105">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="621db-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="621db-106">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="621db-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="621db-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="621db-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="621db-108">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="621db-108">Overview of dependency injection</span></span>

<span data-ttu-id="621db-109">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="621db-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="621db-110">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="621db-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="621db-111">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="621db-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="621db-112">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="621db-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="621db-113">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="621db-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="621db-114">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="621db-115">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="621db-116">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="621db-117">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="621db-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="621db-118">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="621db-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="621db-119">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="621db-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="621db-120">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="621db-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="621db-121">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="621db-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="621db-122">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="621db-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="621db-123">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="621db-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="621db-124">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="621db-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="621db-125">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="621db-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="621db-126">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="621db-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="621db-127">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="621db-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="621db-128">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="621db-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="621db-129">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="621db-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="621db-130">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="621db-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="621db-131">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="621db-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="621db-132">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="621db-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="621db-133">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="621db-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="621db-134">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="621db-135">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="621db-136">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="621db-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="621db-137">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="621db-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="621db-138">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="621db-139">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="621db-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="621db-140">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="621db-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="621db-141">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="621db-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="621db-142">たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="621db-143">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="621db-144">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="621db-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="621db-145">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="621db-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="621db-146">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="621db-147">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="621db-148">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="621db-149">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="621db-149">Services injected into Startup</span></span>

<span data-ttu-id="621db-150">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="621db-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="621db-151">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="621db-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="621db-152">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="621db-153">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="621db-153">Framework-provided services</span></span>

<span data-ttu-id="621db-154">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="621db-155">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="621db-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="621db-156">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="621db-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="621db-157">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="621db-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="621db-158">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="621db-158">Service Type</span></span> | <span data-ttu-id="621db-159">有効期間</span><span class="sxs-lookup"><span data-stu-id="621db-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="621db-160">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="621db-161">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="621db-162">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="621db-163">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="621db-164">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="621db-165">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="621db-166">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="621db-167">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="621db-168">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="621db-169">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="621db-170">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="621db-171">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="621db-172">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="621db-173">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="621db-174">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="621db-174">Register additional services with extension methods</span></span>

<span data-ttu-id="621db-175">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="621db-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="621db-176">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="621db-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="621db-177">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="621db-178">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="621db-178">Service lifetimes</span></span>

<span data-ttu-id="621db-179">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="621db-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="621db-180">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="621db-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="621db-181">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-181">Transient</span></span>

<span data-ttu-id="621db-182">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="621db-183">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="621db-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="621db-184">スコープ</span><span class="sxs-lookup"><span data-stu-id="621db-184">Scoped</span></span>

<span data-ttu-id="621db-185">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="621db-186">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="621db-187">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="621db-188">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="621db-189">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-189">Singleton</span></span>

<span data-ttu-id="621db-190">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="621db-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="621db-191">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="621db-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="621db-192">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="621db-193">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="621db-194">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="621db-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="621db-195">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="621db-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="621db-196">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="621db-196">Service registration methods</span></span>

<span data-ttu-id="621db-197">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="621db-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="621db-198">メソッド</span><span class="sxs-lookup"><span data-stu-id="621db-198">Method</span></span> | <span data-ttu-id="621db-199">自動</span><span class="sxs-lookup"><span data-stu-id="621db-199">Automatic</span></span><br><span data-ttu-id="621db-200">object</span><span class="sxs-lookup"><span data-stu-id="621db-200">object</span></span><br><span data-ttu-id="621db-201">破棄</span><span class="sxs-lookup"><span data-stu-id="621db-201">disposal</span></span> | <span data-ttu-id="621db-202">複数</span><span class="sxs-lookup"><span data-stu-id="621db-202">Multiple</span></span><br><span data-ttu-id="621db-203">実装</span><span class="sxs-lookup"><span data-stu-id="621db-203">implementations</span></span> | <span data-ttu-id="621db-204">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="621db-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="621db-205">例:</span><span class="sxs-lookup"><span data-stu-id="621db-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="621db-206">はい</span><span class="sxs-lookup"><span data-stu-id="621db-206">Yes</span></span> | <span data-ttu-id="621db-207">はい</span><span class="sxs-lookup"><span data-stu-id="621db-207">Yes</span></span> | <span data-ttu-id="621db-208">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="621db-209">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="621db-210">はい</span><span class="sxs-lookup"><span data-stu-id="621db-210">Yes</span></span> | <span data-ttu-id="621db-211">はい</span><span class="sxs-lookup"><span data-stu-id="621db-211">Yes</span></span> | <span data-ttu-id="621db-212">はい</span><span class="sxs-lookup"><span data-stu-id="621db-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="621db-213">例:</span><span class="sxs-lookup"><span data-stu-id="621db-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="621db-214">はい</span><span class="sxs-lookup"><span data-stu-id="621db-214">Yes</span></span> | <span data-ttu-id="621db-215">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-215">No</span></span> | <span data-ttu-id="621db-216">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="621db-217">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="621db-218">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-218">No</span></span> | <span data-ttu-id="621db-219">はい</span><span class="sxs-lookup"><span data-stu-id="621db-219">Yes</span></span> | <span data-ttu-id="621db-220">はい</span><span class="sxs-lookup"><span data-stu-id="621db-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="621db-221">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="621db-222">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-222">No</span></span> | <span data-ttu-id="621db-223">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-223">No</span></span> | <span data-ttu-id="621db-224">はい</span><span class="sxs-lookup"><span data-stu-id="621db-224">Yes</span></span> |

<span data-ttu-id="621db-225">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="621db-226">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="621db-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="621db-227">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="621db-228">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="621db-229">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="621db-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="621db-230">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="621db-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="621db-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="621db-232">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="621db-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="621db-233">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="621db-234">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="621db-235">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="621db-236">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="621db-237">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="621db-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="621db-238">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="621db-238">Constructor injection behavior</span></span>

<span data-ttu-id="621db-239">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="621db-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="621db-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="621db-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="621db-241">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="621db-242">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="621db-243">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="621db-244">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="621db-245">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="621db-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="621db-246">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="621db-246">Entity Framework contexts</span></span>

<span data-ttu-id="621db-247">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="621db-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="621db-248">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="621db-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="621db-249">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="621db-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="621db-250">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="621db-250">Lifetime and registration options</span></span>

<span data-ttu-id="621db-251">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="621db-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="621db-252">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="621db-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="621db-253">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="621db-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="621db-254">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="621db-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="621db-255">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="621db-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="621db-256">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="621db-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="621db-257">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="621db-258">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="621db-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="621db-259">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="621db-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="621db-260">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="621db-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="621db-261">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="621db-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="621db-262">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="621db-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="621db-263">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="621db-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="621db-264">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="621db-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="621db-265">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="621db-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="621db-266">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="621db-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="621db-267">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="621db-268">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="621db-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="621db-269">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="621db-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="621db-270">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="621db-270">**First request:**</span></span>

<span data-ttu-id="621db-271">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="621db-271">Controller operations:</span></span>

<span data-ttu-id="621db-272">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="621db-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="621db-273">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="621db-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="621db-274">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-275">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-276">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="621db-276">`OperationService` operations:</span></span>

<span data-ttu-id="621db-277">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="621db-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="621db-278">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="621db-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="621db-279">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-280">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-281">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="621db-281">**Second request:**</span></span>

<span data-ttu-id="621db-282">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="621db-282">Controller operations:</span></span>

<span data-ttu-id="621db-283">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="621db-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="621db-284">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="621db-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="621db-285">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-286">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-287">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="621db-287">`OperationService` operations:</span></span>

<span data-ttu-id="621db-288">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="621db-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="621db-289">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="621db-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="621db-290">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-291">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-292">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="621db-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="621db-293">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-293">*Transient* objects are always different.</span></span> <span data-ttu-id="621db-294">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="621db-295">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="621db-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="621db-296">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="621db-297">"*シングルトン*" オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="621db-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="621db-298">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="621db-298">Call services from main</span></span>

<span data-ttu-id="621db-299">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="621db-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="621db-300">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="621db-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="621db-301">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="621db-302">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="621db-302">Scope validation</span></span>

<span data-ttu-id="621db-303">アプリが開発環境で実行されていて、ホストを構築するために [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) が呼び出される場合、既定のサービス プロバイダーによって次を確認するためのチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="621db-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="621db-304">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="621db-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="621db-305">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="621db-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="621db-306"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="621db-307">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="621db-308">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="621db-309">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="621db-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="621db-310">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="621db-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="621db-311">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="621db-312">要求サービス</span><span class="sxs-lookup"><span data-stu-id="621db-312">Request Services</span></span>

<span data-ttu-id="621db-313">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="621db-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="621db-314">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="621db-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="621db-315">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="621db-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="621db-316">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="621db-317">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="621db-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="621db-318">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="621db-319">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="621db-320">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="621db-320">Design services for dependency injection</span></span>

<span data-ttu-id="621db-321">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="621db-321">Best practices are to:</span></span>

* <span data-ttu-id="621db-322">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="621db-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="621db-323">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="621db-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="621db-324">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="621db-325">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="621db-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="621db-326">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="621db-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="621db-327">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="621db-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="621db-328">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="621db-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="621db-329">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="621db-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="621db-330">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="621db-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="621db-331">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="621db-332">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="621db-332">Disposal of services</span></span>

<span data-ttu-id="621db-333">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="621db-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="621db-334">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="621db-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="621db-335">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="621db-336">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-336">In the following example:</span></span>

* <span data-ttu-id="621db-337">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="621db-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="621db-338">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="621db-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="621db-339">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="621db-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="621db-340">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="621db-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="621db-341">サービス破棄オプションについては、「[Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)」(ASP.NET Core で IDisposables を破棄する 4 つの方法) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="621db-342">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="621db-342">Default service container replacement</span></span>

<span data-ttu-id="621db-343">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="621db-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="621db-344">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="621db-345">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="621db-345">Property injection</span></span>
* <span data-ttu-id="621db-346">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="621db-346">Injection based on name</span></span>
* <span data-ttu-id="621db-347">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="621db-347">Child containers</span></span>
* <span data-ttu-id="621db-348">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="621db-348">Custom lifetime management</span></span>
* <span data-ttu-id="621db-349">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="621db-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="621db-350">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="621db-350">Convention-based registration</span></span>

<span data-ttu-id="621db-351">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="621db-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="621db-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="621db-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="621db-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="621db-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="621db-354">Grace</span><span class="sxs-lookup"><span data-stu-id="621db-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="621db-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="621db-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="621db-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="621db-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="621db-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="621db-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="621db-358">Unity</span><span class="sxs-lookup"><span data-stu-id="621db-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="621db-359">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="621db-359">Thread safety</span></span>

<span data-ttu-id="621db-360">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="621db-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="621db-361">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="621db-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="621db-362">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="621db-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="621db-363">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="621db-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="621db-364">推奨事項</span><span class="sxs-lookup"><span data-stu-id="621db-364">Recommendations</span></span>

* <span data-ttu-id="621db-365">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="621db-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="621db-366">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="621db-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="621db-367">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="621db-368">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="621db-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="621db-369">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="621db-370">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="621db-371">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="621db-372">サービスへの静的なアクセスを回避します (たとえば、他所で使用するための [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定)。</span><span class="sxs-lookup"><span data-stu-id="621db-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="621db-373">*サービス ロケーター パターン*の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="621db-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="621db-374">たとえば、サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="621db-375">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="621db-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="621db-376">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="621db-376">**Correct**:</span></span>

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

* <span data-ttu-id="621db-377">回避すべき別のサービス ロケーターのバリエーションは、実行時に依存関係を解決するファクトリを挿入することです。</span><span class="sxs-lookup"><span data-stu-id="621db-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="621db-378">この両方のプラクティスによって、複数の[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)方式が組み合わせられます。</span><span class="sxs-lookup"><span data-stu-id="621db-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="621db-379">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="621db-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="621db-380">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="621db-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="621db-381">例外はまれです&mdash;ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="621db-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="621db-382">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="621db-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="621db-383">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="621db-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="621db-384">DI でのマルチテナントの推奨パターン</span><span class="sxs-lookup"><span data-stu-id="621db-384">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="621db-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) はマルチテナント機能があります。</span><span class="sxs-lookup"><span data-stu-id="621db-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="621db-386">詳細については、[Orchard Core のドキュメント](https://docs.orchardcore.net/en/dev/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-386">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="621db-387">CMS 固有の機能を使用せずに Orchard Core Framework のみを使用してモジュラーおよびマルチテナント アプリを構築する方法の例については、 https://github.com/OrchardCMS/OrchardCore.Samples のサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-387">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="621db-388">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="621db-388">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="621db-389">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="621db-389">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="621db-390">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="621db-390">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="621db-391">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="621db-391">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="621db-392">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="621db-392">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="621db-393">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="621db-393">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="621db-394">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="621db-394">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="621db-395">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="621db-395">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="621db-396">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="621db-396">Overview of dependency injection</span></span>

<span data-ttu-id="621db-397">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="621db-397">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="621db-398">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="621db-398">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="621db-399">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="621db-399">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="621db-400">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="621db-400">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="621db-401">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="621db-401">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="621db-402">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-402">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="621db-403">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-403">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="621db-404">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-404">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="621db-405">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="621db-405">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="621db-406">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="621db-406">This implementation is difficult to unit test.</span></span> <span data-ttu-id="621db-407">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="621db-407">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="621db-408">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="621db-408">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="621db-409">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="621db-409">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="621db-410">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="621db-410">Registration of the dependency in a service container.</span></span> <span data-ttu-id="621db-411">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="621db-411">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="621db-412">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="621db-412">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="621db-413">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="621db-413">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="621db-414">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="621db-414">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="621db-415">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="621db-415">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="621db-416">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="621db-416">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="621db-417">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="621db-417">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="621db-418">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="621db-418">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="621db-419">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="621db-419">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="621db-420">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="621db-420">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="621db-421">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="621db-421">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="621db-422">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-422">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="621db-423">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-423">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="621db-424">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="621db-424">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="621db-425">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="621db-425">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="621db-426">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-426">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="621db-427">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="621db-427">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="621db-428">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="621db-428">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="621db-429">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="621db-429">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="621db-430">たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-430">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="621db-431">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-431">We recommended that apps follow this convention.</span></span> <span data-ttu-id="621db-432">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="621db-432">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="621db-433">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="621db-433">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="621db-434">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-434">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="621db-435">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-435">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="621db-436">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-436">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="621db-437">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="621db-437">Services injected into Startup</span></span>

<span data-ttu-id="621db-438">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="621db-438">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="621db-439">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="621db-439">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="621db-440">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-440">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="621db-441">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="621db-441">Framework-provided services</span></span>

<span data-ttu-id="621db-442">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-442">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="621db-443">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="621db-443">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="621db-444">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="621db-444">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="621db-445">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="621db-445">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="621db-446">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="621db-446">Service Type</span></span> | <span data-ttu-id="621db-447">有効期間</span><span class="sxs-lookup"><span data-stu-id="621db-447">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="621db-448">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="621db-449">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="621db-450">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-450">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="621db-451">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-451">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="621db-452">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-452">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="621db-453">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-453">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="621db-454">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="621db-455">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-455">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="621db-456">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-456">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="621db-457">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-457">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="621db-458">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-458">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="621db-459">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-459">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="621db-460">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-460">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="621db-461">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-461">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="621db-462">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="621db-462">Register additional services with extension methods</span></span>

<span data-ttu-id="621db-463">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="621db-463">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="621db-464">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="621db-464">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="621db-465">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-465">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="621db-466">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="621db-466">Service lifetimes</span></span>

<span data-ttu-id="621db-467">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="621db-467">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="621db-468">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="621db-468">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="621db-469">一時的</span><span class="sxs-lookup"><span data-stu-id="621db-469">Transient</span></span>

<span data-ttu-id="621db-470">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-470">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="621db-471">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="621db-471">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="621db-472">スコープ</span><span class="sxs-lookup"><span data-stu-id="621db-472">Scoped</span></span>

<span data-ttu-id="621db-473">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-473">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="621db-474">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-474">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="621db-475">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-475">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="621db-476">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-476">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="621db-477">シングルトン</span><span class="sxs-lookup"><span data-stu-id="621db-477">Singleton</span></span>

<span data-ttu-id="621db-478">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="621db-478">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="621db-479">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="621db-479">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="621db-480">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-480">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="621db-481">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-481">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="621db-482">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="621db-482">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="621db-483">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="621db-483">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="621db-484">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="621db-484">Service registration methods</span></span>

<span data-ttu-id="621db-485">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="621db-485">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="621db-486">メソッド</span><span class="sxs-lookup"><span data-stu-id="621db-486">Method</span></span> | <span data-ttu-id="621db-487">自動</span><span class="sxs-lookup"><span data-stu-id="621db-487">Automatic</span></span><br><span data-ttu-id="621db-488">object</span><span class="sxs-lookup"><span data-stu-id="621db-488">object</span></span><br><span data-ttu-id="621db-489">破棄</span><span class="sxs-lookup"><span data-stu-id="621db-489">disposal</span></span> | <span data-ttu-id="621db-490">複数</span><span class="sxs-lookup"><span data-stu-id="621db-490">Multiple</span></span><br><span data-ttu-id="621db-491">実装</span><span class="sxs-lookup"><span data-stu-id="621db-491">implementations</span></span> | <span data-ttu-id="621db-492">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="621db-492">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="621db-493">例:</span><span class="sxs-lookup"><span data-stu-id="621db-493">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="621db-494">はい</span><span class="sxs-lookup"><span data-stu-id="621db-494">Yes</span></span> | <span data-ttu-id="621db-495">はい</span><span class="sxs-lookup"><span data-stu-id="621db-495">Yes</span></span> | <span data-ttu-id="621db-496">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-496">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="621db-497">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-497">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="621db-498">はい</span><span class="sxs-lookup"><span data-stu-id="621db-498">Yes</span></span> | <span data-ttu-id="621db-499">はい</span><span class="sxs-lookup"><span data-stu-id="621db-499">Yes</span></span> | <span data-ttu-id="621db-500">はい</span><span class="sxs-lookup"><span data-stu-id="621db-500">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="621db-501">例:</span><span class="sxs-lookup"><span data-stu-id="621db-501">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="621db-502">はい</span><span class="sxs-lookup"><span data-stu-id="621db-502">Yes</span></span> | <span data-ttu-id="621db-503">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-503">No</span></span> | <span data-ttu-id="621db-504">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-504">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="621db-505">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-505">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="621db-506">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-506">No</span></span> | <span data-ttu-id="621db-507">はい</span><span class="sxs-lookup"><span data-stu-id="621db-507">Yes</span></span> | <span data-ttu-id="621db-508">はい</span><span class="sxs-lookup"><span data-stu-id="621db-508">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="621db-509">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-509">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="621db-510">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-510">No</span></span> | <span data-ttu-id="621db-511">いいえ</span><span class="sxs-lookup"><span data-stu-id="621db-511">No</span></span> | <span data-ttu-id="621db-512">はい</span><span class="sxs-lookup"><span data-stu-id="621db-512">Yes</span></span> |

<span data-ttu-id="621db-513">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-513">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="621db-514">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="621db-514">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="621db-515">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-515">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="621db-516">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-516">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="621db-517">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="621db-517">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="621db-518">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="621db-518">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="621db-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="621db-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="621db-520">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="621db-520">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="621db-521">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="621db-521">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="621db-522">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-522">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="621db-523">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-523">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="621db-524">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="621db-524">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="621db-525">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="621db-525">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="621db-526">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="621db-526">Constructor injection behavior</span></span>

<span data-ttu-id="621db-527">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="621db-527">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="621db-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="621db-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="621db-529">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="621db-529">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="621db-530">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-530">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="621db-531">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-531">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="621db-532">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-532">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="621db-533">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="621db-533">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="621db-534">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="621db-534">Entity Framework contexts</span></span>

<span data-ttu-id="621db-535">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="621db-535">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="621db-536">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="621db-536">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="621db-537">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="621db-537">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="621db-538">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="621db-538">Lifetime and registration options</span></span>

<span data-ttu-id="621db-539">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="621db-539">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="621db-540">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="621db-540">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="621db-541">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="621db-541">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="621db-542">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="621db-542">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="621db-543">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="621db-543">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="621db-544">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="621db-544">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="621db-545">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-545">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="621db-546">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="621db-546">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="621db-547">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="621db-547">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="621db-548">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="621db-548">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="621db-549">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="621db-549">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="621db-550">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="621db-550">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="621db-551">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="621db-551">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="621db-552">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="621db-552">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="621db-553">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="621db-553">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="621db-554">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="621db-554">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="621db-555">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="621db-555">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="621db-556">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="621db-556">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="621db-557">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="621db-557">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="621db-558">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="621db-558">**First request:**</span></span>

<span data-ttu-id="621db-559">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="621db-559">Controller operations:</span></span>

<span data-ttu-id="621db-560">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="621db-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="621db-561">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="621db-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="621db-562">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-563">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-563">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-564">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="621db-564">`OperationService` operations:</span></span>

<span data-ttu-id="621db-565">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="621db-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="621db-566">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="621db-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="621db-567">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-568">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-568">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-569">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="621db-569">**Second request:**</span></span>

<span data-ttu-id="621db-570">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="621db-570">Controller operations:</span></span>

<span data-ttu-id="621db-571">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="621db-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="621db-572">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="621db-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="621db-573">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-574">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-574">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-575">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="621db-575">`OperationService` operations:</span></span>

<span data-ttu-id="621db-576">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="621db-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="621db-577">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="621db-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="621db-578">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="621db-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="621db-579">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="621db-579">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="621db-580">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="621db-580">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="621db-581">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-581">*Transient* objects are always different.</span></span> <span data-ttu-id="621db-582">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-582">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="621db-583">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="621db-583">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="621db-584">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="621db-584">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="621db-585">"*シングルトン*" オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="621db-585">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="621db-586">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="621db-586">Call services from main</span></span>

<span data-ttu-id="621db-587">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="621db-587">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="621db-588">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="621db-588">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="621db-589">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-589">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="621db-590">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="621db-590">Scope validation</span></span>

<span data-ttu-id="621db-591">アプリが開発環境で実行されている場合、既定のサービス プロバイダーは次を確認するためのチェックを実行します。</span><span class="sxs-lookup"><span data-stu-id="621db-591">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="621db-592">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="621db-592">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="621db-593">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="621db-593">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="621db-594"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-594">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="621db-595">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-595">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="621db-596">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-596">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="621db-597">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="621db-597">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="621db-598">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="621db-598">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="621db-599">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="621db-599">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="621db-600">要求サービス</span><span class="sxs-lookup"><span data-stu-id="621db-600">Request Services</span></span>

<span data-ttu-id="621db-601">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="621db-601">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="621db-602">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="621db-602">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="621db-603">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="621db-603">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="621db-604">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-604">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="621db-605">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="621db-605">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="621db-606">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="621db-606">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="621db-607">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-607">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="621db-608">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="621db-608">Design services for dependency injection</span></span>

<span data-ttu-id="621db-609">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="621db-609">Best practices are to:</span></span>

* <span data-ttu-id="621db-610">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="621db-610">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="621db-611">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="621db-611">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="621db-612">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-612">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="621db-613">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="621db-613">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="621db-614">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="621db-614">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="621db-615">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="621db-615">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="621db-616">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="621db-616">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="621db-617">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="621db-617">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="621db-618">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="621db-618">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="621db-619">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-619">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="621db-620">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="621db-620">Disposal of services</span></span>

<span data-ttu-id="621db-621">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="621db-621">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="621db-622">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="621db-622">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="621db-623">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="621db-623">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="621db-624">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="621db-624">In the following example:</span></span>

* <span data-ttu-id="621db-625">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="621db-625">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="621db-626">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="621db-626">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="621db-627">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="621db-627">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="621db-628">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="621db-628">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="621db-629">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="621db-629">Default service container replacement</span></span>

<span data-ttu-id="621db-630">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="621db-630">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="621db-631">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-631">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="621db-632">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="621db-632">Property injection</span></span>
* <span data-ttu-id="621db-633">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="621db-633">Injection based on name</span></span>
* <span data-ttu-id="621db-634">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="621db-634">Child containers</span></span>
* <span data-ttu-id="621db-635">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="621db-635">Custom lifetime management</span></span>
* <span data-ttu-id="621db-636">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="621db-636">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="621db-637">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="621db-637">Convention-based registration</span></span>

<span data-ttu-id="621db-638">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="621db-638">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="621db-639">Autofac</span><span class="sxs-lookup"><span data-stu-id="621db-639">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="621db-640">DryIoc</span><span class="sxs-lookup"><span data-stu-id="621db-640">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="621db-641">Grace</span><span class="sxs-lookup"><span data-stu-id="621db-641">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="621db-642">LightInject</span><span class="sxs-lookup"><span data-stu-id="621db-642">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="621db-643">Lamar</span><span class="sxs-lookup"><span data-stu-id="621db-643">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="621db-644">Stashbox</span><span class="sxs-lookup"><span data-stu-id="621db-644">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="621db-645">Unity</span><span class="sxs-lookup"><span data-stu-id="621db-645">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="621db-646">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="621db-646">Thread safety</span></span>

<span data-ttu-id="621db-647">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="621db-647">Create thread-safe singleton services.</span></span> <span data-ttu-id="621db-648">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="621db-648">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="621db-649">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService(IServiceCollection, Func\<IServiceProvider,TService)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="621db-649">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="621db-650">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="621db-650">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="621db-651">推奨事項</span><span class="sxs-lookup"><span data-stu-id="621db-651">Recommendations</span></span>

* <span data-ttu-id="621db-652">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="621db-652">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="621db-653">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="621db-653">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="621db-654">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="621db-654">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="621db-655">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="621db-655">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="621db-656">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-656">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="621db-657">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="621db-657">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="621db-658">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="621db-658">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="621db-659">サービスへの静的なアクセスを回避します (たとえば、他所で使用するための [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定)。</span><span class="sxs-lookup"><span data-stu-id="621db-659">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="621db-660">*サービス ロケーター パターン*は、[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)戦略が混在しているので使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-660">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="621db-661">サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-661">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="621db-662">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="621db-662">**Incorrect:**</span></span>

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

    <span data-ttu-id="621db-663">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="621db-663">**Correct**:</span></span>

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

  * <span data-ttu-id="621db-664"><xref:System.IServiceProvider.GetService*> を使用して、実行時に依存関係を解決するファクトリを挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="621db-664">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="621db-665">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="621db-665">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="621db-666">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="621db-666">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="621db-667">例外はまれです&mdash;ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="621db-667">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="621db-668">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="621db-668">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="621db-669">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="621db-669">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="621db-670">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="621db-670">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="621db-671">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="621db-671">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="621db-672">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="621db-672">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="621db-673">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="621db-673">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="621db-674">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="621db-674">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
