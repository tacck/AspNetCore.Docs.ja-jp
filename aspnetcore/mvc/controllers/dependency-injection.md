---
title: ASP.NET Core でのコントローラーへの依存関係の挿入
author: ardalis
description: ASP.NET Core の MVC コントローラーが、ASP.NET Core でそれらのコンストラクターと依存関係の挿入を使用して、明示的にそれらの依存関係を要求する方法について説明します。
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: bae31e38c3b4146ec5e4b7a398a2e0fa290fd34c
ms.sourcegitcommit: 99c784a873b62fbd97a73c5c07f4fe7a7f5db638
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2020
ms.locfileid: "85503540"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="760f3-103">ASP.NET Core でのコントローラーへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="760f3-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="760f3-104">作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="760f3-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="760f3-105">ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="760f3-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="760f3-106">ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="760f3-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="760f3-107">DI を利用すれば、アプリのテストと保守管理が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="760f3-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="760f3-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="760f3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="760f3-109">コンストラクターの挿入</span><span class="sxs-lookup"><span data-stu-id="760f3-109">Constructor Injection</span></span>

<span data-ttu-id="760f3-110">サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="760f3-111">サービスは通常、インターフェイスを利用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="760f3-112">たとえば、現在の時刻を必要とするアプリについて考えてください。</span><span class="sxs-lookup"><span data-stu-id="760f3-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="760f3-113">次のインターフェイスは `IDateTime` サービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="760f3-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="760f3-114">次のコードは `IDateTime` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="760f3-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="760f3-115">サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="760f3-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="760f3-116"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="760f3-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="760f3-117">次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="760f3-118">アプリを実行すると、時刻に基づいてメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="760f3-119">FromServices によるアクションの挿入</span><span class="sxs-lookup"><span data-stu-id="760f3-119">Action injection with FromServices</span></span>

<span data-ttu-id="760f3-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="760f3-121">コントローラーから設定にアクセスする</span><span class="sxs-lookup"><span data-stu-id="760f3-121">Access settings from a controller</span></span>

<span data-ttu-id="760f3-122">コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="760f3-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="760f3-123"><xref:fundamentals/configuration/options> で説明されている*オプション パターン*が設定の管理手法として推奨されています。</span><span class="sxs-lookup"><span data-stu-id="760f3-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="760f3-124">通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="760f3-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="760f3-125">オプションを表すクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="760f3-125">Create a class that represents the options.</span></span> <span data-ttu-id="760f3-126">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="760f3-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="760f3-127">サービス コレクションに構成クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="760f3-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="760f3-128">JSON 形式ファイルから設定を読み込むようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="760f3-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="760f3-129">次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。</span><span class="sxs-lookup"><span data-stu-id="760f3-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="760f3-130">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="760f3-130">Additional resources</span></span>

* <span data-ttu-id="760f3-131">コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="760f3-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="760f3-132">[既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="760f3-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="760f3-133">作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="760f3-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="760f3-134">ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="760f3-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="760f3-135">ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="760f3-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="760f3-136">DI を利用すれば、アプリのテストと保守管理が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="760f3-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="760f3-137">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="760f3-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="760f3-138">コンストラクターの挿入</span><span class="sxs-lookup"><span data-stu-id="760f3-138">Constructor Injection</span></span>

<span data-ttu-id="760f3-139">サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="760f3-140">サービスは通常、インターフェイスを利用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="760f3-141">たとえば、現在の時刻を必要とするアプリについて考えてください。</span><span class="sxs-lookup"><span data-stu-id="760f3-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="760f3-142">次のインターフェイスは `IDateTime` サービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="760f3-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="760f3-143">次のコードは `IDateTime` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="760f3-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="760f3-144">サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="760f3-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="760f3-145"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="760f3-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="760f3-146">次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="760f3-147">アプリを実行すると、時刻に基づいてメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="760f3-148">FromServices によるアクションの挿入</span><span class="sxs-lookup"><span data-stu-id="760f3-148">Action injection with FromServices</span></span>

<span data-ttu-id="760f3-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。</span><span class="sxs-lookup"><span data-stu-id="760f3-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="760f3-150">コントローラーから設定にアクセスする</span><span class="sxs-lookup"><span data-stu-id="760f3-150">Access settings from a controller</span></span>

<span data-ttu-id="760f3-151">コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="760f3-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="760f3-152"><xref:fundamentals/configuration/options> で説明されている*オプション パターン*が設定の管理手法として推奨されています。</span><span class="sxs-lookup"><span data-stu-id="760f3-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="760f3-153">通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="760f3-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="760f3-154">オプションを表すクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="760f3-154">Create a class that represents the options.</span></span> <span data-ttu-id="760f3-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="760f3-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="760f3-156">サービス コレクションに構成クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="760f3-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="760f3-157">JSON 形式ファイルから設定を読み込むようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="760f3-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="760f3-158">次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。</span><span class="sxs-lookup"><span data-stu-id="760f3-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="760f3-159">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="760f3-159">Additional resources</span></span>

* <span data-ttu-id="760f3-160">コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="760f3-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="760f3-161">[既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="760f3-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end