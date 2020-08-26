---
title: ASP.NET Core での依存関係の挿入
author: rick-anderson
description: ASP.NET Core で依存関係の挿入を実装する方法とそれを使う方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628795"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="616a7-103">ASP.NET Core での依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="616a7-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="616a7-104">作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="616a7-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="616a7-105">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="616a7-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="616a7-106">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="616a7-107">オプションとしての依存関係挿入の詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="616a7-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="616a7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="616a7-109">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="616a7-109">Overview of dependency injection</span></span>

<span data-ttu-id="616a7-110">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="616a7-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="616a7-111">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="616a7-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="616a7-112">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="616a7-113">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="616a7-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="616a7-114">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="616a7-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="616a7-115">コードの依存関係には、前の例のような問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="616a7-116">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="616a7-117">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="616a7-118">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="616a7-119">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="616a7-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="616a7-120">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="616a7-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="616a7-121">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="616a7-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="616a7-122">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="616a7-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="616a7-123">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="616a7-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="616a7-124">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="616a7-125">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="616a7-126">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="616a7-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="616a7-127">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="616a7-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="616a7-128">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="616a7-129">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="616a7-130">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="616a7-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> メソッドによって、サービスは、1 つの要求の有効期間であるスコープ付き有効期間で登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="616a7-132">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="616a7-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="616a7-133">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="616a7-134">DI パターンの場合:</span><span class="sxs-lookup"><span data-stu-id="616a7-134">With the DI pattern:</span></span>

* <span data-ttu-id="616a7-135">コントローラーでは、具象型 `MyDependency` は使用されず、インターフェイス `IMyDependency` のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="616a7-136">これにより、コントローラーが使用する実装は、コントローラーを変更することなく、簡単に変更できるようになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="616a7-137">`MyDependency` のインスタンスは、コントローラーによって作成されることはなく、DI コンテナーによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="616a7-138">組み込みのログ API を使用すると、`IMyDependency` インターフェイスの実装を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="616a7-139">更新された `ConfigureServices` メソッドでは、新しい `IMyDependency` の実装が登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="616a7-140">コンストラクターでは、`MyDependency2` によって <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="616a7-141">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="616a7-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="616a7-142">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="616a7-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="616a7-143">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="616a7-144">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="616a7-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="616a7-145">`ILogger<TCategoryName>` は、[フレームワークで提供されるサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="616a7-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="616a7-146">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="616a7-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="616a7-147">依存関係の挿入に関する用語では、サービスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="616a7-148">通常、アプリ内の他のコードにサービスを提供するオブジェクトです (`IMyDependency` サービスなど)。</span><span class="sxs-lookup"><span data-stu-id="616a7-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="616a7-149">Web サービスを使用する場合はありますが、サービスは Web サービスに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="616a7-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="616a7-150">フレームワークは、堅牢な [ログ](xref:fundamentals/logging/index) システムを備えています。</span><span class="sxs-lookup"><span data-stu-id="616a7-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="616a7-151">`IMyDependency` の実装は、ログを実装するのではなく、基本的な DI を実演するために記述されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="616a7-152">ほとんどのアプリでは、ロガーを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="616a7-153">次のコードでは、既定のログを使用する方法が示されます。この場合、`ConfigureServices` にサービスを登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="616a7-154">前のコードを使用すると、[ログ](xref:fundamentals/logging/index) がフレームワークによって提供されるため、`ConfigureServices` を更新する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="616a7-155">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="616a7-155">Services injected into Startup</span></span>

<span data-ttu-id="616a7-156">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="616a7-157">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="616a7-158">詳細については、「<xref:fundamentals/startup>」および「[起動時の構成へのアクセス](xref:fundamentals/configuration/index#access-configuration-in-startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="616a7-159">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="616a7-159">Register additional services with extension methods</span></span>

<span data-ttu-id="616a7-160">サービスを登録するためにサービス コレクション拡張メソッドを使用できる場合:</span><span class="sxs-lookup"><span data-stu-id="616a7-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="616a7-161">規則は、単一の `Add{SERVICE_NAME}` 拡張メソッドを使用して、そのサービスに必要なすべてのサービスを登録するというものです。</span><span class="sxs-lookup"><span data-stu-id="616a7-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="616a7-162">依存サービスも登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-162">The dependent services are also registered.</span></span>

<span data-ttu-id="616a7-163">次のコードは、個々のユーザー アカウントを使用して Razor ページ テンプレートに基づいて生成されており、拡張メソッド <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> および <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> を使用してコンテナーにさらにサービスを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="616a7-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="616a7-164">詳細については、次のトピックを参照してください。 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> および <xref:security/authentication/identity></span><span class="sxs-lookup"><span data-stu-id="616a7-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="616a7-165">サービスを登録する拡張メソッドを記述する方法については、「[サービス コレクションの結合](#csc)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="616a7-166">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-166">Service lifetimes</span></span>

<span data-ttu-id="616a7-167">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="616a7-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="616a7-168">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="616a7-169">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-169">Transient</span></span>

<span data-ttu-id="616a7-170">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="616a7-171">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="616a7-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="616a7-172">要求を処理するアプリでは、一時的なサービスが要求の最後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="616a7-173">スコープ</span><span class="sxs-lookup"><span data-stu-id="616a7-173">Scoped</span></span>

<span data-ttu-id="616a7-174">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="616a7-175">Entity Framework Core を使用する場合、既定では <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張メソッドによって、スコープ付き有効期間を持つ `DbContext` 型が登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="616a7-176">要求を処理するアプリでは、スコープ付きサービスは要求の最後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="616a7-177">次のいずれかの方法を使用して、ミドルウェアでスコープ付きサービスを使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="616a7-178">`Invoke` または `InvokeAsync` メソッドにサービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="616a7-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="616a7-179">[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入では、サービスがシングルトンのように動作するように強制されるため、実行時に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="616a7-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="616a7-180">「[有効期間と登録のオプション](#lifetime-and-registration-options)」にあるサンプルでは、`InvokeAsync` によるアプローチが使用されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="616a7-181">[ファクトリ ベースのミドルウェア](<xref:fundamentals/middleware/extensibility>)</span><span class="sxs-lookup"><span data-stu-id="616a7-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="616a7-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 拡張メソッドでは、ミドルウェアの登録済みの型で <xref:Microsoft.AspNetCore.Http.IMiddleware> が実装されているかが確認されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="616a7-183">実装されている場合、規則に基づくミドルウェアのライセンス認証ロジックを使用する代わりに、コンテナーに登録されている <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> インスタンスが <xref:Microsoft.AspNetCore.Http.IMiddleware> の実装の解決に使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="616a7-184">ミドルウェアは、アプリのサービス コンテナーで、スコープ化されたまたは一時的なサービスとして登録されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="616a7-185">詳細については、 <xref:fundamentals/middleware/write#per-request-middleware-dependencies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="616a7-186">シングルトンからスコープ付きサービスを解決***しない***でください。</span><span class="sxs-lookup"><span data-stu-id="616a7-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="616a7-187">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="616a7-188">次の場合は問題ありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-188">It's fine to:</span></span>

* <span data-ttu-id="616a7-189">スコープ付きまたは一時的なサービスからシングルトン サービスを解決する。</span><span class="sxs-lookup"><span data-stu-id="616a7-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="616a7-190">スコープ付きサービスを、別のスコープ付きまたは一時的なサービスから解決する。</span><span class="sxs-lookup"><span data-stu-id="616a7-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="616a7-191">既定では、開発環境で、あるサービスをより長い有効期間を持つ別のサービスからサービスを解決すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="616a7-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="616a7-192">詳しくは、「[スコープの検証](#sv)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="616a7-193">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-193">Singleton</span></span>

<span data-ttu-id="616a7-194">シングルトン有効期間サービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) が作成されるのは、次のいずれかの場合です。</span><span class="sxs-lookup"><span data-stu-id="616a7-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="616a7-195">それらが初めて要求された場合。</span><span class="sxs-lookup"><span data-stu-id="616a7-195">The first time they're requested.</span></span>
* <span data-ttu-id="616a7-196">開発者によって、実装インスタンスがコンテナーに直接提供される場合。</span><span class="sxs-lookup"><span data-stu-id="616a7-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="616a7-197">このアプローチはほとんど必要ありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-197">This approach is rarely needed.</span></span>

<span data-ttu-id="616a7-198">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="616a7-199">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="616a7-200">シングルトン デザイン パターンを実装したり、シングルトンを破棄するコードを提供したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="616a7-201">コンテナーからサービスを解決したコードによって、サービスが破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="616a7-202">型またはファクトリがシングルトンとして登録されている場合、コンテナーによってシングルトンが自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="616a7-203">シングルトン サービスはスレッド セーフである必要があり、ほとんどの場合、ステートレス サービスで使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="616a7-204">要求を処理するアプリでは、アプリのシャットダウン時に <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> が破棄されるとき、シングルトン サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="616a7-205">アプリがシャットダウンされるまでメモリは解放されないため、シングルトンでのメモリ使用を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="616a7-206">シングルトンからスコープ付きサービスを解決***しない***でください。</span><span class="sxs-lookup"><span data-stu-id="616a7-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="616a7-207">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="616a7-208">スコープ付きまたは一時的なサービスからシングルトン サービスを解決することに問題はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="616a7-209">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="616a7-209">Service registration methods</span></span>

<span data-ttu-id="616a7-210">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="616a7-211">メソッド</span><span class="sxs-lookup"><span data-stu-id="616a7-211">Method</span></span> | <span data-ttu-id="616a7-212">自動</span><span class="sxs-lookup"><span data-stu-id="616a7-212">Automatic</span></span><br><span data-ttu-id="616a7-213">object</span><span class="sxs-lookup"><span data-stu-id="616a7-213">object</span></span><br><span data-ttu-id="616a7-214">破棄</span><span class="sxs-lookup"><span data-stu-id="616a7-214">disposal</span></span> | <span data-ttu-id="616a7-215">複数</span><span class="sxs-lookup"><span data-stu-id="616a7-215">Multiple</span></span><br><span data-ttu-id="616a7-216">実装</span><span class="sxs-lookup"><span data-stu-id="616a7-216">implementations</span></span> | <span data-ttu-id="616a7-217">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="616a7-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="616a7-218">例:</span><span class="sxs-lookup"><span data-stu-id="616a7-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="616a7-219">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-219">Yes</span></span> | <span data-ttu-id="616a7-220">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-220">Yes</span></span> | <span data-ttu-id="616a7-221">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-222">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="616a7-223">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-223">Yes</span></span> | <span data-ttu-id="616a7-224">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-224">Yes</span></span> | <span data-ttu-id="616a7-225">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="616a7-226">例:</span><span class="sxs-lookup"><span data-stu-id="616a7-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="616a7-227">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-227">Yes</span></span> | <span data-ttu-id="616a7-228">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-228">No</span></span> | <span data-ttu-id="616a7-229">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-230">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="616a7-231">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-231">No</span></span> | <span data-ttu-id="616a7-232">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-232">Yes</span></span> | <span data-ttu-id="616a7-233">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-234">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="616a7-235">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-235">No</span></span> | <span data-ttu-id="616a7-236">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-236">No</span></span> | <span data-ttu-id="616a7-237">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-237">Yes</span></span> |

<span data-ttu-id="616a7-238">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="616a7-239">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="616a7-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="616a7-240">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="616a7-241">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="616a7-242">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="616a7-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="616a7-243">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="616a7-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="616a7-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="616a7-245">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="616a7-246">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にインスタンスを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="616a7-247">一般に、ライブラリの作成者は、コンテナー内の実装の複数のコピーを登録しないようにするため `TryAddEnumerable` を使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="616a7-248">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="616a7-249">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="616a7-250">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="616a7-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="616a7-251">サービスの登録は、通常、同じ種類の複数の実装を登録する場合を除き、順序に依存しません。</span><span class="sxs-lookup"><span data-stu-id="616a7-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="616a7-252">`IServiceCollection` は、<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> のコレクションです。</span><span class="sxs-lookup"><span data-stu-id="616a7-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="616a7-253">次のコードは、コンストラクターを使用してサービスを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="616a7-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="616a7-254">`Add{LIFETIME}` メソッドでも同じアプローチが使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="616a7-255">たとえば、[AddScoped のソース コード](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="616a7-256">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="616a7-256">Constructor injection behavior</span></span>

<span data-ttu-id="616a7-257">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="616a7-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="616a7-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="616a7-259">依存関係の挿入コンテナーにサービスを登録することなくオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="616a7-260">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、MVC コントローラー、[モデル バインダー](xref:mvc/models/model-binding)などのフレームワーク機能で使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="616a7-261">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="616a7-262">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="616a7-263">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="616a7-264">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="616a7-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="616a7-265">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="616a7-265">Entity Framework contexts</span></span>

<span data-ttu-id="616a7-266">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="616a7-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="616a7-267">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="616a7-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="616a7-268">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="616a7-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="616a7-269">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="616a7-269">Lifetime and registration options</span></span>

<span data-ttu-id="616a7-270">有効期間と登録のオプションの違いを示すために、タスクを識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="616a7-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="616a7-271">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーからは、クラスによって要求されたときに、サービスの同じインスタンスか別のインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="616a7-272">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="616a7-273">`Operation` コンストラクターでは、GUID の最後の 4 文字が指定されていない場合、それが生成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="616a7-274">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="616a7-275">サンプル アプリでは、それぞれの要求内、およびそれぞれの要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="616a7-276">サンプル アプリの `IndexModel` およびミドルウェア要求では、各種の `IOperation` 型が必要であり、`OperationId` がログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="616a7-277">ミドルウェアは `IndexModel` に類似していて、同じサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="616a7-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="616a7-278">スコープ付きのサービスは `InvokeAsync` メソッドで解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="616a7-279">ロガーの出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-279">The logger output shows:</span></span>

* <span data-ttu-id="616a7-280">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-280">*Transient* objects are always different.</span></span> <span data-ttu-id="616a7-281">一時的な `OperationId` 値は、`IndexModel` とミドルウェアでは異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="616a7-282">"*スコープ付きの*" オブジェクトは、それぞれの要求内では同じですが、それぞれの要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="616a7-283">"*シングルトン*" オブジェクトは、すべての要求において同じです。</span><span class="sxs-lookup"><span data-stu-id="616a7-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="616a7-284">ログ出力を減らすには、*appsettings.Development.json* ファイル内で "Logging:LogLevel:Microsoft:Error" を設定します。</span><span class="sxs-lookup"><span data-stu-id="616a7-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="616a7-285">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="616a7-285">Call services from main</span></span>

<span data-ttu-id="616a7-286">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="616a7-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="616a7-287">このアプローチは、起動時に初期化タスクを実行するために、スコープ付きのサービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="616a7-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="616a7-288">上記のコードは、Razor ページのチュートリアルの「[シード初期化子の追加](xref:tutorials/razor-pages/sql?#add-the-seed-initializer)」からのものです。</span><span class="sxs-lookup"><span data-stu-id="616a7-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="616a7-289">次の例では、`Program.Main` で `IMyDependency` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="616a7-290">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="616a7-290">Scope validation</span></span>

<span data-ttu-id="616a7-291">アプリが[開発環境](xref:fundamentals/environments)で実行されていて、ホストを構築するために [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) が呼び出される場合、既定のサービス プロバイダーによって次を確認するためのチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="616a7-292">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="616a7-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="616a7-293">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="616a7-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="616a7-294">一時的なサービスが、シングルトンまたはスコープ付きサービスに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="616a7-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="616a7-295"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="616a7-296">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリの有効期間に対応し、アプリのシャットダウン時には破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="616a7-297">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="616a7-298">ルート コンテナーにスコープ付きサービスが作成されると、サービスはアプリのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="616a7-299">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="616a7-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="616a7-300">詳しくは、「[スコープの検証](xref:fundamentals/host/web-host#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="616a7-301">要求サービス</span><span class="sxs-lookup"><span data-stu-id="616a7-301">Request Services</span></span>

<span data-ttu-id="616a7-302">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="616a7-303">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="616a7-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="616a7-304">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="616a7-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="616a7-305">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="616a7-306">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="616a7-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="616a7-307">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="616a7-308">ASP.NET Core では要求ごとにスコープが作成され、`RequestServices` によってスコープ サービス プロバイダーが公開されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="616a7-309">すべてのスコープ サービスは、要求がアクティブである限り有効です。</span><span class="sxs-lookup"><span data-stu-id="616a7-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="616a7-310">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="616a7-311">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="616a7-311">Design services for dependency injection</span></span>

<span data-ttu-id="616a7-312">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="616a7-312">Best practices are to:</span></span>

* <span data-ttu-id="616a7-313">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="616a7-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="616a7-314">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="616a7-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="616a7-315">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="616a7-316">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="616a7-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="616a7-317">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="616a7-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="616a7-318">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="616a7-319">クラスに含まれる挿入される依存関係が多すぎるように見える場合、それは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="616a7-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="616a7-320">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="616a7-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="616a7-321">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="616a7-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="616a7-322">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="616a7-322">Disposal of services</span></span>

<span data-ttu-id="616a7-323">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="616a7-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="616a7-324">コンテナーからサービスを解決したコードによって、サービスが破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="616a7-325">型またはファクトリがシングルトンとして登録されている場合、コンテナーによってシングルトンが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="616a7-326">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="616a7-327">デバッグ コンソールでは、インデックス ページを更新するたびに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="616a7-328">サービス コンテナーによって作成されていないサービス</span><span class="sxs-lookup"><span data-stu-id="616a7-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="616a7-329">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="616a7-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="616a7-330">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="616a7-330">In the preceding code:</span></span>

* <span data-ttu-id="616a7-331">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="616a7-332">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="616a7-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="616a7-333">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="616a7-334">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="616a7-335">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="616a7-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="616a7-336">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-336">Transient, limited lifetime</span></span>

<span data-ttu-id="616a7-337">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="616a7-337">**Scenario**</span></span>

<span data-ttu-id="616a7-338">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="616a7-339">インスタンスは、ルート スコープ (ルート コンテナー) で解決されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="616a7-340">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="616a7-341">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="616a7-341">**Solution**</span></span>

<span data-ttu-id="616a7-342">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="616a7-343">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="616a7-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="616a7-344">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="616a7-345">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="616a7-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="616a7-346"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="616a7-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="616a7-347">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="616a7-348">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="616a7-348">**Scenario**</span></span>

<span data-ttu-id="616a7-349">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> の有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="616a7-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="616a7-350">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="616a7-350">**Solution**</span></span>

<span data-ttu-id="616a7-351">インスタンスをスコープ付きの有効期間に登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="616a7-352"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用してを開始し、新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="616a7-353">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="616a7-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="616a7-354">有効期間が終了するときにスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="616a7-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="616a7-355">IDisposable の一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="616a7-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="616a7-356"><xref:System.IDisposable> インスタンスは、一時的なスコープを使用して登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="616a7-357">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="616a7-358">一時的またはスコープ付きの <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="616a7-359">唯一の一般的な例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合です。これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="616a7-360">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="616a7-361"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="616a7-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="616a7-362">サービスの有効期間を制御するには、スコープを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="616a7-363">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="616a7-364">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="616a7-364">Default service container replacement</span></span>

<span data-ttu-id="616a7-365">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="616a7-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="616a7-366">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="616a7-367">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="616a7-367">Property injection</span></span>
* <span data-ttu-id="616a7-368">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="616a7-368">Injection based on name</span></span>
* <span data-ttu-id="616a7-369">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="616a7-369">Child containers</span></span>
* <span data-ttu-id="616a7-370">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="616a7-370">Custom lifetime management</span></span>
* <span data-ttu-id="616a7-371">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="616a7-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="616a7-372">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="616a7-372">Convention-based registration</span></span>

<span data-ttu-id="616a7-373">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="616a7-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="616a7-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="616a7-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="616a7-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="616a7-376">Grace</span><span class="sxs-lookup"><span data-stu-id="616a7-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="616a7-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="616a7-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="616a7-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="616a7-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="616a7-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="616a7-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="616a7-380">Unity</span><span class="sxs-lookup"><span data-stu-id="616a7-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="616a7-381">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="616a7-381">Thread safety</span></span>

<span data-ttu-id="616a7-382">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="616a7-383">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="616a7-384">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="616a7-385">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="616a7-386">推奨事項</span><span class="sxs-lookup"><span data-stu-id="616a7-386">Recommendations</span></span>

* <span data-ttu-id="616a7-387">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="616a7-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="616a7-388">C# では非同期コンストラクターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="616a7-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="616a7-389">推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="616a7-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="616a7-390">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="616a7-391">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="616a7-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="616a7-392">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="616a7-393">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="616a7-394">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="616a7-395">サービスへの静的なアクセスを行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-395">Avoid static access to services.</span></span> <span data-ttu-id="616a7-396">たとえば、他の場所で使用するために [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定を行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="616a7-397">DI ファクトリを高速かつ同期的に維持します。</span><span class="sxs-lookup"><span data-stu-id="616a7-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="616a7-398">*サービス ロケーター パターン*の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="616a7-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="616a7-399">たとえば、サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="616a7-400">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="616a7-400">**Incorrect:**</span></span>

    ![正しくないコード](dependency-injection/_static/bad.png)

  <span data-ttu-id="616a7-402">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="616a7-402">**Correct**:</span></span>

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
* <span data-ttu-id="616a7-403">回避すべき別のサービス ロケーターのバリエーションは、実行時に依存関係を解決するファクトリを挿入することです。</span><span class="sxs-lookup"><span data-stu-id="616a7-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="616a7-404">この両方のプラクティスによって、複数の[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)方式が組み合わせられます。</span><span class="sxs-lookup"><span data-stu-id="616a7-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="616a7-405">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="616a7-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="616a7-406">`ConfigureServices` で <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> を呼び出すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="616a7-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="616a7-407">`BuildServiceProvider` の呼び出しは、通常、開発者が `ConfigureServices` でサービスを解決する必要がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="616a7-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="616a7-408">たとえば、構成から `LoginPath` を取得する必要がある場合を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="616a7-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="616a7-409">次のコードは避けてください。</span><span class="sxs-lookup"><span data-stu-id="616a7-409">Avoid the following code:</span></span>

  ![BuildServiceProvider を呼び出すコードが正しくありません](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="616a7-411">上の図では、`services.BuildServiceProvider` の下の緑の波線を選択すると、次の ASP0000 警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="616a7-412">アプリケーション コードから ASP0000 呼び出し 'BuildServiceProvider' を行うと、シングルトン サービスの追加のコピーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="616a7-413">'Configure' のパラメーターとして依存関係挿入サービスなどの代替手段を検討してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="616a7-414">`BuildServiceProvider` を呼び出すと、2 つ目のコンテナーが作成されます。これを使用すれば、破損したシングルトンが作成され、複数のコンテナーにまたがるオブジェクト グラフへの参照を生じさせることができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="616a7-415">`LoginPath` を取得する正しい方法は、DI でオプション パターンを使用することです。</span><span class="sxs-lookup"><span data-stu-id="616a7-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="616a7-416">破棄可能な一時的なサービスは、破棄のためにコンテナーによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="616a7-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="616a7-417">これにより、最上位のコンテナーから解決された場合、メモリ リークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="616a7-418">スコープの検証を有効にすることで、アプリにシングルトンをキャプチャするスコープ付きサービスがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="616a7-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="616a7-419">詳しくは、「[スコープの検証](#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="616a7-420">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="616a7-421">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="616a7-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="616a7-422">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="616a7-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="616a7-423">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="616a7-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="616a7-424">DI でのマルチテナントの推奨パターン</span><span class="sxs-lookup"><span data-stu-id="616a7-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="616a7-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) はマルチテナント機能があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="616a7-426">詳細については、[Orchard Core のドキュメント](https://docs.orchardcore.net/en/dev/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="616a7-427">CMS 固有の機能を使用せずに Orchard Core Framework のみを使用してモジュラーおよびマルチテナント アプリを構築する方法の例については、 https://github.com/OrchardCMS/OrchardCore.Samples のサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="616a7-428">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="616a7-428">Framework-provided services</span></span>

<span data-ttu-id="616a7-429">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="616a7-430">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="616a7-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="616a7-431">ASP.NET Core テンプレートに基づくアプリには、フレームワークによって 250 を超えるサービスが登録されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="616a7-432">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="616a7-433">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="616a7-433">Service Type</span></span> | <span data-ttu-id="616a7-434">有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="616a7-435">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="616a7-436">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="616a7-437">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="616a7-438">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="616a7-439">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="616a7-440">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="616a7-441">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="616a7-442">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="616a7-443">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="616a7-444">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="616a7-445">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="616a7-446">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="616a7-447">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="616a7-448">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="616a7-449">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="616a7-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="616a7-450">NDC カンファレンス DI アプリ開発のパターン</span><span class="sxs-lookup"><span data-stu-id="616a7-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="616a7-451">ASP.NET Core で IDisposables を破棄する 4 つの方法</span><span class="sxs-lookup"><span data-stu-id="616a7-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="616a7-452">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="616a7-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="616a7-453">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="616a7-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="616a7-454">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="616a7-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="616a7-455">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="616a7-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="616a7-456">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="616a7-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="616a7-457">ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。</span><span class="sxs-lookup"><span data-stu-id="616a7-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="616a7-458">MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="616a7-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="616a7-459">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="616a7-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="616a7-460">依存関係の挿入の概要</span><span class="sxs-lookup"><span data-stu-id="616a7-460">Overview of dependency injection</span></span>

<span data-ttu-id="616a7-461">"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。</span><span class="sxs-lookup"><span data-stu-id="616a7-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="616a7-462">アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="616a7-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="616a7-463">クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="616a7-464">`MyDependency` クラスは `IndexModel` クラスの依存関係です。</span><span class="sxs-lookup"><span data-stu-id="616a7-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="616a7-465">このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。</span><span class="sxs-lookup"><span data-stu-id="616a7-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="616a7-466">コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="616a7-467">`MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="616a7-468">`MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="616a7-469">複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="616a7-470">このような実装では、単体テストを行うことが困難です。</span><span class="sxs-lookup"><span data-stu-id="616a7-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="616a7-471">アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。</span><span class="sxs-lookup"><span data-stu-id="616a7-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="616a7-472">依存関係の挿入は、次の方法によってこれらの問題に対応します。</span><span class="sxs-lookup"><span data-stu-id="616a7-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="616a7-473">依存関係の実装を抽象化するための、インターフェイスまたは基底クラスの使用。</span><span class="sxs-lookup"><span data-stu-id="616a7-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="616a7-474">サービス コンテナー内の依存関係の登録。</span><span class="sxs-lookup"><span data-stu-id="616a7-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="616a7-475">ASP.NET Core には、組み込みのサービス コンテナー <xref:System.IServiceProvider> が用意されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="616a7-476">サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。</span><span class="sxs-lookup"><span data-stu-id="616a7-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="616a7-477">サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。</span><span class="sxs-lookup"><span data-stu-id="616a7-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="616a7-478">依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。</span><span class="sxs-lookup"><span data-stu-id="616a7-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="616a7-479">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="616a7-480">このインターフェイスは、具象型 `MyDependency` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="616a7-481">`MyDependency` では、コンストラクター内で <xref:Microsoft.Extensions.Logging.ILogger`1> が要求されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="616a7-482">依存関係の挿入をチェーン形式で使用することはよくあります。</span><span class="sxs-lookup"><span data-stu-id="616a7-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="616a7-483">次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="616a7-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="616a7-484">コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="616a7-485">解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="616a7-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="616a7-486">`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="616a7-487">`IMyDependency` は `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="616a7-488">`ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。</span><span class="sxs-lookup"><span data-stu-id="616a7-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="616a7-489">コンテナーでは、[(ジェネリック) オープン型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)を活用し、すべての [(ジェネリック) 構築型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)を登録する必要をなくすことで、`ILogger<TCategoryName>` を解決します。</span><span class="sxs-lookup"><span data-stu-id="616a7-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="616a7-490">サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="616a7-491">登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="616a7-492">[サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="616a7-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="616a7-493">各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。</span><span class="sxs-lookup"><span data-stu-id="616a7-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="616a7-494">たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="616a7-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="616a7-495">アプリをこの規則に従わせることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="616a7-496">拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="616a7-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="616a7-497">サービスのコンストラクターで[ビルトイン型](/dotnet/csharp/language-reference/keywords/built-in-types-table) (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使って型を挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="616a7-498">クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="616a7-499">このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="616a7-500">サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="616a7-501">Startup に挿入されるサービス</span><span class="sxs-lookup"><span data-stu-id="616a7-501">Services injected into Startup</span></span>

<span data-ttu-id="616a7-502">汎用ホスト (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) を使用すると、次のサービスの種類のみを `Startup` コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="616a7-503">サービスは `Startup.Configure` に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="616a7-504">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="616a7-505">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="616a7-505">Framework-provided services</span></span>

<span data-ttu-id="616a7-506">`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="616a7-507">最初に、`ConfigureServices` に提供される `IServiceCollection` には、フレームワークによって定義されたサービスがあります ([ホストの構成方法](xref:fundamentals/index#host)によって異なります)。</span><span class="sxs-lookup"><span data-stu-id="616a7-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="616a7-508">ASP.NET Core テンプレートに基づくアプリが、フレームワークによって登録された何百ものサービスを持つことも珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="616a7-509">次の表に、フレームワークによって登録されたサービスのごく一部のサンプルを示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="616a7-510">サービスの種類</span><span class="sxs-lookup"><span data-stu-id="616a7-510">Service Type</span></span> | <span data-ttu-id="616a7-511">有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="616a7-512">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="616a7-513">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="616a7-514">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="616a7-515">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="616a7-516">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="616a7-517">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="616a7-518">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="616a7-519">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="616a7-520">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="616a7-521">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="616a7-522">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="616a7-523">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="616a7-524">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="616a7-525">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="616a7-526">拡張メソッドを使用した追加サービスの登録する</span><span class="sxs-lookup"><span data-stu-id="616a7-526">Register additional services with extension methods</span></span>

<span data-ttu-id="616a7-527">サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。</span><span class="sxs-lookup"><span data-stu-id="616a7-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="616a7-528">次のコードは、拡張メソッド [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) と <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> を使用して、コンテナーに追加のサービスを追加する方法の例です。</span><span class="sxs-lookup"><span data-stu-id="616a7-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="616a7-529">詳細については、API ドキュメントにある <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> クラスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="616a7-530">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-530">Service lifetimes</span></span>

<span data-ttu-id="616a7-531">登録される各サービスの適切な有効期間を選択します。</span><span class="sxs-lookup"><span data-stu-id="616a7-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="616a7-532">ASP.NET Core サービスは、次の有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="616a7-533">一時的</span><span class="sxs-lookup"><span data-stu-id="616a7-533">Transient</span></span>

<span data-ttu-id="616a7-534">有効期間が一時的なサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) は、サービス コンテナーから要求されるたびに作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="616a7-535">この有効期間は、軽量でステートレスのサービスに最適です。</span><span class="sxs-lookup"><span data-stu-id="616a7-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="616a7-536">要求を処理するアプリでは、一時的なサービスが要求の最後に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="616a7-537">スコープ</span><span class="sxs-lookup"><span data-stu-id="616a7-537">Scoped</span></span>

<span data-ttu-id="616a7-538">有効期間がスコープのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) は、クライアント要求 (接続) ごとに 1 回作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="616a7-539">要求を処理するアプリでは、スコープ付きサービスは要求の最後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="616a7-540">ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="616a7-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="616a7-541">コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用した挿入は行わないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="616a7-542">詳細については、「<xref:fundamentals/middleware/write#per-request-middleware-dependencies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="616a7-543">シングルトン</span><span class="sxs-lookup"><span data-stu-id="616a7-543">Singleton</span></span>

<span data-ttu-id="616a7-544">有効期間がシングルトンのサービス (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) は、最初に要求されたときに作成されます (または、`Startup.ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。</span><span class="sxs-lookup"><span data-stu-id="616a7-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="616a7-545">以降の要求は、すべて同じインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="616a7-546">アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="616a7-547">クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="616a7-548">要求を処理するアプリでは、<xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> がアプリのシャットダウン時に破棄されたとき、シングルトン サービスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="616a7-549">シングルトンからスコープ サービスを解決するのは危険です。</span><span class="sxs-lookup"><span data-stu-id="616a7-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="616a7-550">後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="616a7-551">サービス登録メソッド</span><span class="sxs-lookup"><span data-stu-id="616a7-551">Service registration methods</span></span>

<span data-ttu-id="616a7-552">サービス登録拡張メソッドでは、特定のシナリオで役立つオーバーロードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="616a7-553">メソッド</span><span class="sxs-lookup"><span data-stu-id="616a7-553">Method</span></span> | <span data-ttu-id="616a7-554">自動</span><span class="sxs-lookup"><span data-stu-id="616a7-554">Automatic</span></span><br><span data-ttu-id="616a7-555">object</span><span class="sxs-lookup"><span data-stu-id="616a7-555">object</span></span><br><span data-ttu-id="616a7-556">破棄</span><span class="sxs-lookup"><span data-stu-id="616a7-556">disposal</span></span> | <span data-ttu-id="616a7-557">複数</span><span class="sxs-lookup"><span data-stu-id="616a7-557">Multiple</span></span><br><span data-ttu-id="616a7-558">実装</span><span class="sxs-lookup"><span data-stu-id="616a7-558">implementations</span></span> | <span data-ttu-id="616a7-559">引数を渡す</span><span class="sxs-lookup"><span data-stu-id="616a7-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="616a7-560">例:</span><span class="sxs-lookup"><span data-stu-id="616a7-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="616a7-561">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-561">Yes</span></span> | <span data-ttu-id="616a7-562">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-562">Yes</span></span> | <span data-ttu-id="616a7-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-564">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="616a7-565">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-565">Yes</span></span> | <span data-ttu-id="616a7-566">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-566">Yes</span></span> | <span data-ttu-id="616a7-567">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="616a7-568">例:</span><span class="sxs-lookup"><span data-stu-id="616a7-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="616a7-569">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-569">Yes</span></span> | <span data-ttu-id="616a7-570">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-570">No</span></span> | <span data-ttu-id="616a7-571">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-572">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="616a7-573">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-573">No</span></span> | <span data-ttu-id="616a7-574">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-574">Yes</span></span> | <span data-ttu-id="616a7-575">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="616a7-576">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="616a7-577">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-577">No</span></span> | <span data-ttu-id="616a7-578">いいえ</span><span class="sxs-lookup"><span data-stu-id="616a7-578">No</span></span> | <span data-ttu-id="616a7-579">はい</span><span class="sxs-lookup"><span data-stu-id="616a7-579">Yes</span></span> |

<span data-ttu-id="616a7-580">型の廃棄の詳細については、「[サービスの破棄](#disposal-of-services)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="616a7-581">実装が複数の場合の一般的なシナリオとしては、[テスト用に型のモックを作成](xref:test/integration-tests#inject-mock-services)します。</span><span class="sxs-lookup"><span data-stu-id="616a7-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="616a7-582">`TryAdd{LIFETIME}` メソッドでは、登録された実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="616a7-583">次の例の最初の行では、`IMyDependency` に `MyDependency` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="616a7-584">2 行目では何も行われません。`IMyDependency` には登録された実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="616a7-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="616a7-585">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="616a7-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="616a7-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) メソッドでは、"*同じ型*" の実装がまだ存在しない場合にのみサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="616a7-587">複数のサービスは、`IEnumerable<{SERVICE}>` によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="616a7-588">サービスを登録するとき、開発者は同じ型のものがまだ追加されていない場合にのみインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="616a7-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="616a7-589">一般に、このメソッドは、インスタンスのコピーが 2 つコンテナーに登録されるのを回避するために、ライブラリ作成者によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="616a7-590">次の例の最初の行では、`IMyDep1` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="616a7-591">2 行目では `IMyDep2` に `MyDep` を登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="616a7-592">3 行目では何も行われません。`IMyDep1` には `MyDep` の登録済みの実装が既に含まれているからです。</span><span class="sxs-lookup"><span data-stu-id="616a7-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="616a7-593">コンストラクターの挿入の動作</span><span class="sxs-lookup"><span data-stu-id="616a7-593">Constructor injection behavior</span></span>

<span data-ttu-id="616a7-594">サービスは 2 つのメカニズムによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="616a7-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="616a7-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="616a7-596">`ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="616a7-597">コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="616a7-598">`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、"*パブリック*" コンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="616a7-599">`ActivatorUtilities` によってサービスを解決する場合、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)には、該当するコンストラクターが 1 つだけ存在することが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="616a7-600">コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。</span><span class="sxs-lookup"><span data-stu-id="616a7-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="616a7-601">Entity Framework コンテキスト</span><span class="sxs-lookup"><span data-stu-id="616a7-601">Entity Framework contexts</span></span>

<span data-ttu-id="616a7-602">Entity Framework コンテキストでは通常、[範囲が指定された有効期間](#service-lifetimes)が利用され、サービス コンテナーに追加されます。これは、Web アプリ データベース操作は通常、その範囲がクライアント要求に設定されるためです。</span><span class="sxs-lookup"><span data-stu-id="616a7-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="616a7-603">データベース コンテキストの登録時、[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。</span><span class="sxs-lookup"><span data-stu-id="616a7-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="616a7-604">有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。</span><span class="sxs-lookup"><span data-stu-id="616a7-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="616a7-605">有効期間と登録のオプション</span><span class="sxs-lookup"><span data-stu-id="616a7-605">Lifetime and registration options</span></span>

<span data-ttu-id="616a7-606">有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。</span><span class="sxs-lookup"><span data-stu-id="616a7-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="616a7-607">次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="616a7-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="616a7-608">インターフェイスは `Operation` クラス内で実装されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="616a7-609">指定されない場合、`Operation` コンストラクターは GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="616a7-610">`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。</span><span class="sxs-lookup"><span data-stu-id="616a7-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="616a7-611">依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="616a7-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="616a7-612">コンテナーからの要求時に一時的なサービスが作成されるとき、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="616a7-613">`OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="616a7-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="616a7-614">新しいインスタンスは異なる `OperationId` を生成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="616a7-615">クライアント要求ごとにスコープ サービスが作成されるとき、`IOperationScoped` サービスの `OperationId` は、クライアント要求内の `OperationService` のものと同じになります。</span><span class="sxs-lookup"><span data-stu-id="616a7-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="616a7-616">クライアント要求間で、両方のサービスは異なる `OperationId` 値を共有します。</span><span class="sxs-lookup"><span data-stu-id="616a7-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="616a7-617">シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべてのクライアント要求とすべてのサービス間で使用されるとき、`OperationId` はすべてのサービス要求の間で一定です。</span><span class="sxs-lookup"><span data-stu-id="616a7-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="616a7-618">`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="616a7-619">`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="616a7-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="616a7-620">この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。</span><span class="sxs-lookup"><span data-stu-id="616a7-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="616a7-621">サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="616a7-622">サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="616a7-623">次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="616a7-624">2 つの要求の結果を、以下の 2 つの出力に示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="616a7-625">**最初の要求:**</span><span class="sxs-lookup"><span data-stu-id="616a7-625">**First request:**</span></span>

<span data-ttu-id="616a7-626">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="616a7-626">Controller operations:</span></span>

<span data-ttu-id="616a7-627">一時的: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="616a7-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="616a7-628">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="616a7-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="616a7-629">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="616a7-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="616a7-630">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="616a7-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="616a7-631">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="616a7-631">`OperationService` operations:</span></span>

<span data-ttu-id="616a7-632">一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="616a7-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="616a7-633">スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="616a7-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="616a7-634">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="616a7-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="616a7-635">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="616a7-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="616a7-636">**2 番目の要求:**</span><span class="sxs-lookup"><span data-stu-id="616a7-636">**Second request:**</span></span>

<span data-ttu-id="616a7-637">コントローラーの操作:</span><span class="sxs-lookup"><span data-stu-id="616a7-637">Controller operations:</span></span>

<span data-ttu-id="616a7-638">一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="616a7-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="616a7-639">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="616a7-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="616a7-640">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="616a7-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="616a7-641">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="616a7-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="616a7-642">`OperationService` の操作:</span><span class="sxs-lookup"><span data-stu-id="616a7-642">`OperationService` operations:</span></span>

<span data-ttu-id="616a7-643">一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="616a7-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="616a7-644">スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="616a7-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="616a7-645">シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="616a7-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="616a7-646">インスタンス:00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="616a7-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="616a7-647">要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="616a7-648">"*一時的な*" オブジェクトは常に異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-648">*Transient* objects are always different.</span></span> <span data-ttu-id="616a7-649">1 番目と 2 番目のクライアント要求の一時的な `OperationId` 値は、`OperationService` 操作とクライアント要求間の両方に対して異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="616a7-650">個々のサービス要求とクライアント要求に対して、新しいインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="616a7-651">*Scoped* オブジェクトは、1 つのクライアント要求内では同じですが、複数のクライアント要求間では異なります。</span><span class="sxs-lookup"><span data-stu-id="616a7-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="616a7-652">"*シングルトン*" オブジェクトは、`Operation` インスタンスが `Startup.ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。</span><span class="sxs-lookup"><span data-stu-id="616a7-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="616a7-653">main からサービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="616a7-653">Call services from main</span></span>

<span data-ttu-id="616a7-654">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) を使用して <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成し、アプリのスコープ内のスコープ サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="616a7-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="616a7-655">この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。</span><span class="sxs-lookup"><span data-stu-id="616a7-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="616a7-656">次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="616a7-657">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="616a7-657">Scope validation</span></span>

<span data-ttu-id="616a7-658">アプリが開発環境で実行されている場合、既定のサービス プロバイダーは次を確認するためのチェックを実行します。</span><span class="sxs-lookup"><span data-stu-id="616a7-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="616a7-659">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="616a7-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="616a7-660">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="616a7-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="616a7-661"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="616a7-662">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="616a7-663">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="616a7-664">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="616a7-665">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="616a7-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="616a7-666">詳細については、「<xref:fundamentals/host/web-host#scope-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="616a7-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="616a7-667">要求サービス</span><span class="sxs-lookup"><span data-stu-id="616a7-667">Request Services</span></span>

<span data-ttu-id="616a7-668">`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) コレクションを通じて公開されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="616a7-669">要求サービスは、アプリの一部として構成および要求されるサービスを表します。</span><span class="sxs-lookup"><span data-stu-id="616a7-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="616a7-670">オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。</span><span class="sxs-lookup"><span data-stu-id="616a7-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="616a7-671">一般に、アプリから直接これらのプロパティを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="616a7-672">代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。</span><span class="sxs-lookup"><span data-stu-id="616a7-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="616a7-673">これにより、テストしやすいクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="616a7-674">コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="616a7-675">依存関係の挿入のためのサービスの設計</span><span class="sxs-lookup"><span data-stu-id="616a7-675">Design services for dependency injection</span></span>

<span data-ttu-id="616a7-676">ベスト プラクティスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="616a7-676">Best practices are to:</span></span>

* <span data-ttu-id="616a7-677">各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。</span><span class="sxs-lookup"><span data-stu-id="616a7-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="616a7-678">ステートフル、静的クラス、およびメンバーは避けてください。</span><span class="sxs-lookup"><span data-stu-id="616a7-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="616a7-679">代わりにシングルトン サービスを使用するようにアプリを設計し、グローバルな状態を作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="616a7-680">サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。</span><span class="sxs-lookup"><span data-stu-id="616a7-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="616a7-681">直接のインスタンス化は、コードの固有の実装につながります。</span><span class="sxs-lookup"><span data-stu-id="616a7-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="616a7-682">アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="616a7-683">クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。</span><span class="sxs-lookup"><span data-stu-id="616a7-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="616a7-684">責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。</span><span class="sxs-lookup"><span data-stu-id="616a7-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="616a7-685">Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。</span><span class="sxs-lookup"><span data-stu-id="616a7-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="616a7-686">ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="616a7-687">サービスの破棄</span><span class="sxs-lookup"><span data-stu-id="616a7-687">Disposal of services</span></span>

<span data-ttu-id="616a7-688">コンテナーは、作成する <xref:System.IDisposable> 型の <xref:System.IDisposable.Dispose*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="616a7-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="616a7-689">ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。</span><span class="sxs-lookup"><span data-stu-id="616a7-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="616a7-690">次の例では、サービスがサービス コンテナーによって作成され、自動的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="616a7-691">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="616a7-691">In the following example:</span></span>

* <span data-ttu-id="616a7-692">サービス インスタンスは、サービス コンテナーによって作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="616a7-693">サービスの有効期間は、フレームワークによって認識されません。</span><span class="sxs-lookup"><span data-stu-id="616a7-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="616a7-694">フレームワークでは、サービスが自動的に破棄されることはありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="616a7-695">サービスが開発者コードで明示的に破棄されていない場合は、アプリがシャットダウンするまで保持されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="616a7-696">一時的なインスタンスと共有インスタンスのための IDisposable ガイダンス</span><span class="sxs-lookup"><span data-stu-id="616a7-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="616a7-697">一時的で限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-697">Transient, limited lifetime</span></span>

<span data-ttu-id="616a7-698">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="616a7-698">**Scenario**</span></span>

<span data-ttu-id="616a7-699">アプリには、次のいずれかのシナリオに対して一時的な有効期間を持つ <xref:System.IDisposable> インスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="616a7-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="616a7-700">インスタンスは、ルート スコープで解決されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="616a7-701">インスタンスは、スコープが終了する前に破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="616a7-702">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="616a7-702">**Solution**</span></span>

<span data-ttu-id="616a7-703">ファクトリ パターンを使用して、親スコープの外部にインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="616a7-704">このような状況では、通常、アプリケーションには、最終的な型のコンストラクターを直接呼び出す `Create` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="616a7-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="616a7-705">最終的な型に他の依存関係がある場合、ファクトリは次のことができます。</span><span class="sxs-lookup"><span data-stu-id="616a7-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="616a7-706">そのコンストラクターで <xref:System.IServiceProvider> を受け取る。</span><span class="sxs-lookup"><span data-stu-id="616a7-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="616a7-707"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> を使用してコンテナー外部でインスタンスをインスタンス化し、同時にコンテナーを依存関係のために使用する。</span><span class="sxs-lookup"><span data-stu-id="616a7-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="616a7-708">共有インスタンス、限定的な有効期間</span><span class="sxs-lookup"><span data-stu-id="616a7-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="616a7-709">**シナリオ**</span><span class="sxs-lookup"><span data-stu-id="616a7-709">**Scenario**</span></span>

<span data-ttu-id="616a7-710">アプリでは、複数のサービスにわたって共有 <xref:System.IDisposable> インスタンスが必要ですが、<xref:System.IDisposable> の有効期間は限られています。</span><span class="sxs-lookup"><span data-stu-id="616a7-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="616a7-711">**解決方法**</span><span class="sxs-lookup"><span data-stu-id="616a7-711">**Solution**</span></span>

<span data-ttu-id="616a7-712">インスタンスをスコープ付きの有効期間に登録します。</span><span class="sxs-lookup"><span data-stu-id="616a7-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="616a7-713"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> を使用してを開始し、新しい <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> を作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="616a7-714">スコープの <xref:System.IServiceProvider> を使用して必要なサービスを取得します。</span><span class="sxs-lookup"><span data-stu-id="616a7-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="616a7-715">有効期間が終了するときにスコープを破棄します。</span><span class="sxs-lookup"><span data-stu-id="616a7-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="616a7-716">一般的なガイドライン</span><span class="sxs-lookup"><span data-stu-id="616a7-716">General Guidelines</span></span>

* <span data-ttu-id="616a7-717"><xref:System.IDisposable> インスタンスは、一時的なスコープを使用して登録しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="616a7-718">代わりに、ファクトリ パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="616a7-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="616a7-719">一時的またはスコープ付きの <xref:System.IDisposable> インスタンスをルート スコープ内で解決しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="616a7-720">唯一の一般的な例外は、アプリが <xref:System.IServiceProvider> を作成/再作成し、破棄する場合です。これは理想的なパターンではありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="616a7-721">DI を使用して <xref:System.IDisposable> 依存関係を受け取る場合、受信側が <xref:System.IDisposable> 自体を実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="616a7-722"><xref:System.IDisposable> 依存関係の受信側は、その依存関係で <xref:System.IDisposable.Dispose%2A> を呼び出してはなりません。</span><span class="sxs-lookup"><span data-stu-id="616a7-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="616a7-723">サービスの有効期間を制御するには、スコープを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="616a7-724">スコープは階層構造ではなく、スコープ間に特別な接続はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="616a7-725">既定のサービス コンテナーの置換</span><span class="sxs-lookup"><span data-stu-id="616a7-725">Default service container replacement</span></span>

<span data-ttu-id="616a7-726">組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを目的としたものです。</span><span class="sxs-lookup"><span data-stu-id="616a7-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="616a7-727">組み込みコンテナーでサポートされない、以下のような特定の機能が必要な場合を除き、組み込みコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="616a7-728">プロパティの挿入</span><span class="sxs-lookup"><span data-stu-id="616a7-728">Property injection</span></span>
* <span data-ttu-id="616a7-729">名前に基づく挿入</span><span class="sxs-lookup"><span data-stu-id="616a7-729">Injection based on name</span></span>
* <span data-ttu-id="616a7-730">子コンテナー</span><span class="sxs-lookup"><span data-stu-id="616a7-730">Child containers</span></span>
* <span data-ttu-id="616a7-731">有効期間のカスタム管理</span><span class="sxs-lookup"><span data-stu-id="616a7-731">Custom lifetime management</span></span>
* <span data-ttu-id="616a7-732">遅延初期化に対する `Func<T>` のサポート</span><span class="sxs-lookup"><span data-stu-id="616a7-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="616a7-733">規則に基づく登録</span><span class="sxs-lookup"><span data-stu-id="616a7-733">Convention-based registration</span></span>

<span data-ttu-id="616a7-734">次のサードパーティ コンテナーは ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="616a7-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="616a7-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="616a7-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="616a7-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="616a7-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="616a7-737">Grace</span><span class="sxs-lookup"><span data-stu-id="616a7-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="616a7-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="616a7-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="616a7-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="616a7-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="616a7-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="616a7-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="616a7-741">Unity</span><span class="sxs-lookup"><span data-stu-id="616a7-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="616a7-742">スレッド セーフ</span><span class="sxs-lookup"><span data-stu-id="616a7-742">Thread safety</span></span>

<span data-ttu-id="616a7-743">スレッド セーフのシングルトン サービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="616a7-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="616a7-744">シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="616a7-745">1 つのサービスのファクトリ メソッド (例: [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) に対する 2 番目の引数) をスレッド セーフにする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="616a7-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="616a7-746">型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="616a7-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="616a7-747">推奨事項</span><span class="sxs-lookup"><span data-stu-id="616a7-747">Recommendations</span></span>

* <span data-ttu-id="616a7-748">`async/await` および `Task` ベースのサービスの解決はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="616a7-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="616a7-749">C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="616a7-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="616a7-750">データと構成をサービス コンテナーに直接格納しないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="616a7-751">たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。</span><span class="sxs-lookup"><span data-stu-id="616a7-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="616a7-752">構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="616a7-753">同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="616a7-754">実際のアイテムを DI 経由で要求することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="616a7-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="616a7-755">サービスへの静的なアクセスを行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-755">Avoid static access to services.</span></span> <span data-ttu-id="616a7-756">たとえば、他の場所で使用するために [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) の静的な型指定を行わないようにします。</span><span class="sxs-lookup"><span data-stu-id="616a7-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="616a7-757">*サービス ロケーター パターン*は、[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)戦略が混在しているので使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="616a7-758">サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="616a7-759">**正しくない:**</span><span class="sxs-lookup"><span data-stu-id="616a7-759">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="616a7-760">**正しい**:</span><span class="sxs-lookup"><span data-stu-id="616a7-760">**Correct**:</span></span>

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

* <span data-ttu-id="616a7-761"><xref:System.IServiceProvider.GetService*> を使用して、実行時に依存関係を解決するファクトリを挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="616a7-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="616a7-762">`HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="616a7-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="616a7-763">どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="616a7-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="616a7-764">例外はまれです。ほとんどがフレームワーク自体の内の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="616a7-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="616a7-765">DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。</span><span class="sxs-lookup"><span data-stu-id="616a7-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="616a7-766">静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。</span><span class="sxs-lookup"><span data-stu-id="616a7-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="616a7-767">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="616a7-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="616a7-768">ASP.NET Core で IDisposables を破棄する 4 つの方法</span><span class="sxs-lookup"><span data-stu-id="616a7-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="616a7-769">依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="616a7-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="616a7-770">明示的な依存関係の原則</span><span class="sxs-lookup"><span data-stu-id="616a7-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="616a7-771">制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="616a7-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="616a7-772">ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法</span><span class="sxs-lookup"><span data-stu-id="616a7-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
