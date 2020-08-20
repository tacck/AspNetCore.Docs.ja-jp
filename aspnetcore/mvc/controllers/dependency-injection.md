---
title: ASP.NET Core でのコントローラーへの依存関係の挿入
author: ardalis
description: ASP.NET Core の MVC コントローラーが、ASP.NET Core でそれらのコンストラクターと依存関係の挿入を使用して、明示的にそれらの依存関係を要求する方法について説明します。
ms.author: riande
ms.date: 02/24/2019
no-loc:
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 74c43e4f01e5b0d6923bf06f8859341c49088ef6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635243"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="5fcd5-103">ASP.NET Core でのコントローラーへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="5fcd5-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5fcd5-104">作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="5fcd5-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="5fcd5-105">ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="5fcd5-106">ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5fcd5-107">DI を利用すれば、アプリのテストと保守管理が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="5fcd5-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="5fcd5-109">コンストラクターの挿入</span><span class="sxs-lookup"><span data-stu-id="5fcd5-109">Constructor Injection</span></span>

<span data-ttu-id="5fcd5-110">サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="5fcd5-111">サービスは通常、インターフェイスを利用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="5fcd5-112">たとえば、現在の時刻を必要とするアプリについて考えてください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="5fcd5-113">次のインターフェイスは `IDateTime` サービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="5fcd5-114">次のコードは `IDateTime` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="5fcd5-115">サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="5fcd5-116"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="5fcd5-117">次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5fcd5-118">アプリを実行すると、時刻に基づいてメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="5fcd5-119">FromServices によるアクションの挿入</span><span class="sxs-lookup"><span data-stu-id="5fcd5-119">Action injection with FromServices</span></span>

<span data-ttu-id="5fcd5-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="5fcd5-121">コントローラーから設定にアクセスする</span><span class="sxs-lookup"><span data-stu-id="5fcd5-121">Access settings from a controller</span></span>

<span data-ttu-id="5fcd5-122">コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="5fcd5-123"><xref:fundamentals/configuration/options> で説明されている*オプション パターン*が設定の管理手法として推奨されています。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="5fcd5-124">通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="5fcd5-125">オプションを表すクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-125">Create a class that represents the options.</span></span> <span data-ttu-id="5fcd5-126">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="5fcd5-127">サービス コレクションに構成クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="5fcd5-128">JSON 形式ファイルから設定を読み込むようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="5fcd5-129">次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5fcd5-130">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5fcd5-130">Additional resources</span></span>

* <span data-ttu-id="5fcd5-131">コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="5fcd5-132">[既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5fcd5-133">作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="5fcd5-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="5fcd5-134">ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="5fcd5-135">ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5fcd5-136">DI を利用すれば、アプリのテストと保守管理が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="5fcd5-137">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="5fcd5-138">コンストラクターの挿入</span><span class="sxs-lookup"><span data-stu-id="5fcd5-138">Constructor Injection</span></span>

<span data-ttu-id="5fcd5-139">サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="5fcd5-140">サービスは通常、インターフェイスを利用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="5fcd5-141">たとえば、現在の時刻を必要とするアプリについて考えてください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="5fcd5-142">次のインターフェイスは `IDateTime` サービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="5fcd5-143">次のコードは `IDateTime` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="5fcd5-144">サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="5fcd5-145"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="5fcd5-146">次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5fcd5-147">アプリを実行すると、時刻に基づいてメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="5fcd5-148">FromServices によるアクションの挿入</span><span class="sxs-lookup"><span data-stu-id="5fcd5-148">Action injection with FromServices</span></span>

<span data-ttu-id="5fcd5-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="5fcd5-150">コントローラーから設定にアクセスする</span><span class="sxs-lookup"><span data-stu-id="5fcd5-150">Access settings from a controller</span></span>

<span data-ttu-id="5fcd5-151">コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="5fcd5-152"><xref:fundamentals/configuration/options> で説明されている*オプション パターン*が設定の管理手法として推奨されています。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="5fcd5-153">通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="5fcd5-154">オプションを表すクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-154">Create a class that represents the options.</span></span> <span data-ttu-id="5fcd5-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="5fcd5-156">サービス コレクションに構成クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="5fcd5-157">JSON 形式ファイルから設定を読み込むようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="5fcd5-158">次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5fcd5-159">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5fcd5-159">Additional resources</span></span>

* <span data-ttu-id="5fcd5-160">コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="5fcd5-161">[既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="5fcd5-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end