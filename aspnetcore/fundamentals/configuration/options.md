---
title: ASP.NET Core のオプション パターン
author: rick-anderson
description: ASP.NET Core アプリの関連のある設定のグループを表すオプション パターンを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: efce2caf37534823016c12b298afd277bab22030
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769937"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="7cf9b-103">ASP.NET Core のオプション パターン</span><span class="sxs-lookup"><span data-stu-id="7cf9b-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7cf9b-104">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7cf9b-105">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7cf9b-106">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7cf9b-107">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7cf9b-108">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7cf9b-109">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7cf9b-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="7cf9b-111">Package</span><span class="sxs-lookup"><span data-stu-id="7cf9b-111">Package</span></span>

<span data-ttu-id="7cf9b-112">ASP.NET Core アプリでは、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージが暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="7cf9b-113">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="7cf9b-113">Options interfaces</span></span>

<span data-ttu-id="7cf9b-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="7cf9b-116">変更通知</span><span class="sxs-lookup"><span data-stu-id="7cf9b-116">Change notifications</span></span>
* [<span data-ttu-id="7cf9b-117">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="7cf9b-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="7cf9b-118">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="7cf9b-119">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="7cf9b-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="7cf9b-120">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7cf9b-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7cf9b-122"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7cf9b-123">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7cf9b-124"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7cf9b-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7cf9b-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7cf9b-128"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="7cf9b-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7cf9b-130">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="7cf9b-131"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="7cf9b-132">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="7cf9b-133">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="7cf9b-134">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-134">General options configuration</span></span>

<span data-ttu-id="7cf9b-135">サンプル アプリの例 1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="7cf9b-136">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7cf9b-137">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="7cf9b-138">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="7cf9b-139">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-140">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-141">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-142">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="7cf9b-143">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="7cf9b-144">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="7cf9b-145"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="7cf9b-146">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="7cf9b-147">サンプル アプリの例 2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7cf9b-148">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-148">Use a delegate to set options values.</span></span> <span data-ttu-id="7cf9b-149">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="7cf9b-150">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-151">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf9b-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-153">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="7cf9b-154">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="7cf9b-155">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="7cf9b-156"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="7cf9b-157">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="7cf9b-158">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="7cf9b-159">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="7cf9b-160">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-160">Suboptions configuration</span></span>

<span data-ttu-id="7cf9b-161">サンプル アプリの例 3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="7cf9b-162">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="7cf9b-163">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="7cf9b-164">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7cf9b-165">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="7cf9b-166">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-167">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-168">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7cf9b-169">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="7cf9b-170">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-171">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-172">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="7cf9b-173">オプションの挿入</span><span class="sxs-lookup"><span data-stu-id="7cf9b-173">Options injection</span></span>

<span data-ttu-id="7cf9b-174">オプションの挿入は、サンプル アプリの例 4 に示されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="7cf9b-175"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を次に挿入します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="7cf9b-176">[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを持つ Razor ページまたは MVC ビュー。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="7cf9b-177">ページまたはビュー モデル。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-177">A page or view model.</span></span>

<span data-ttu-id="7cf9b-178">サンプル アプリの次の例では、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> をページ モデル (*Pages/Index.cshtml.cs*) に挿入しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="7cf9b-179">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="7cf9b-180">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-180">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="7cf9b-182">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="7cf9b-183">サンプル アプリの例 5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="7cf9b-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="7cf9b-185">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="7cf9b-186">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="7cf9b-187">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="7cf9b-188">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="7cf9b-189">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="7cf9b-190">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="7cf9b-191">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="7cf9b-192">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="7cf9b-193">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-194">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="7cf9b-195">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="7cf9b-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="7cf9b-196">サンプル アプリの例 6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="7cf9b-197">名前付きオプションをサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="7cf9b-198">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="7cf9b-199">名前付きオプションでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-200">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-201">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="7cf9b-202">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-203">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="7cf9b-204">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="7cf9b-205">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="7cf9b-206">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="7cf9b-207">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="7cf9b-208">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="7cf9b-209">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="7cf9b-210">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="7cf9b-211">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-211">All options are named instances.</span></span> <span data-ttu-id="7cf9b-212">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7cf9b-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-214"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7cf9b-215">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7cf9b-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="7cf9b-216">OptionsBuilder API</span></span>

<span data-ttu-id="7cf9b-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-218">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7cf9b-219">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7cf9b-220">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-220">Use DI services to configure options</span></span>

<span data-ttu-id="7cf9b-221">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7cf9b-222">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7cf9b-223">`OptionsBuilder<TOptions>` から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7cf9b-224"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7cf9b-225">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7cf9b-226">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7cf9b-227">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="7cf9b-228">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="7cf9b-228">Options validation</span></span>

<span data-ttu-id="7cf9b-229">オプションが構成されている場合は、オプションの検証を使用してオプションを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="7cf9b-230">オプションが有効なら `true` を、無効なら `false` を返す検証メソッドと共に、`Validate` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="7cf9b-231">前の例では、名前付きのオプションのインスタンスを `optionalOptionsName` に設定しました。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="7cf9b-232">既定のオプションのインスタンスは `Options.DefaultName` です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="7cf9b-233">オプションのインスタンスが作成されると、検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="7cf9b-234">オプションのインスタンスが最初にアクセスされる際は、検証に合格することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7cf9b-235">オプションの検証によって、オプションのインスタンスが作成された後のオプションの変更を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="7cf9b-236">たとえば、`IOptionsSnapshot` オプションは、オプションが最初にアクセスされたときに要求ごとに 1 回作成されて検証されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="7cf9b-237">`IOptionsSnapshot` オプションが、"*同じ要求の*" 後続のアクセス試行に対して検証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="7cf9b-238">`Validate` メソッドは、`Func<TOptions, bool>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="7cf9b-239">検証を完全にカスタマイズするには、`IValidateOptions<TOptions>` を実装します。これにより、次が可能になります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="7cf9b-240">複数のオプションの種類の検証: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="7cf9b-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="7cf9b-241">別のオプションの種類に基づく検証: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="7cf9b-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="7cf9b-242">`IValidateOptions` は次を検証します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="7cf9b-243">特定の名前付きのオプションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-243">A specific named options instance.</span></span>
* <span data-ttu-id="7cf9b-244">`name` が `null` の場合はすべてのオプション。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="7cf9b-245">インターフェイスの実装から `ValidateOptionsResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="7cf9b-246">データ注釈に基づく検証は、`OptionsBuilder<TOptions>` で <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> メソッドを呼び出すことにより、[Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) パッケージから利用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="7cf9b-247">ASP.NET Core アプリでは、`Microsoft.Extensions.Options.DataAnnotations` が暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="7cf9b-248">先行検証 (起動時にフェイル ファストする) は今後のリリースでの導入が検討されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="7cf9b-249">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-249">Options post-configuration</span></span>

<span data-ttu-id="7cf9b-250">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-251">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-253">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7cf9b-254">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-254">Accessing options during startup</span></span>

<span data-ttu-id="7cf9b-255">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7cf9b-256">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7cf9b-257">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7cf9b-258">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7cf9b-259">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7cf9b-260">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7cf9b-261">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7cf9b-262">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7cf9b-263">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7cf9b-264">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7cf9b-265">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7cf9b-265">Prerequisites</span></span>

<span data-ttu-id="7cf9b-266">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="7cf9b-267">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="7cf9b-267">Options interfaces</span></span>

<span data-ttu-id="7cf9b-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="7cf9b-270">変更通知</span><span class="sxs-lookup"><span data-stu-id="7cf9b-270">Change notifications</span></span>
* [<span data-ttu-id="7cf9b-271">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="7cf9b-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="7cf9b-272">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="7cf9b-273">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="7cf9b-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="7cf9b-274">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7cf9b-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7cf9b-276"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7cf9b-277">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7cf9b-278"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7cf9b-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-280"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7cf9b-281"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7cf9b-282"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="7cf9b-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7cf9b-284">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="7cf9b-285"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="7cf9b-286">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="7cf9b-287">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="7cf9b-288">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-288">General options configuration</span></span>

<span data-ttu-id="7cf9b-289">サンプル アプリの例 1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="7cf9b-290">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7cf9b-291">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="7cf9b-292">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="7cf9b-293">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-294">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-295">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-296">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="7cf9b-297">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="7cf9b-298">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="7cf9b-299"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="7cf9b-300">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="7cf9b-301">サンプル アプリの例 2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7cf9b-302">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-302">Use a delegate to set options values.</span></span> <span data-ttu-id="7cf9b-303">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="7cf9b-304">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-305">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf9b-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-307">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="7cf9b-308">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="7cf9b-309">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="7cf9b-310"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="7cf9b-311">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="7cf9b-312">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="7cf9b-313">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="7cf9b-314">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-314">Suboptions configuration</span></span>

<span data-ttu-id="7cf9b-315">サンプル アプリの例 3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="7cf9b-316">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="7cf9b-317">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="7cf9b-318">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7cf9b-319">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="7cf9b-320">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-321">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-322">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7cf9b-323">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="7cf9b-324">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-325">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-326">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="7cf9b-327">オプションの挿入</span><span class="sxs-lookup"><span data-stu-id="7cf9b-327">Options injection</span></span>

<span data-ttu-id="7cf9b-328">オプションの挿入は、サンプル アプリの例 4 に示されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="7cf9b-329"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を次に挿入します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="7cf9b-330">[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを持つ Razor ページまたは MVC ビュー。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="7cf9b-331">ページまたはビュー モデル。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-331">A page or view model.</span></span>

<span data-ttu-id="7cf9b-332">サンプル アプリの次の例では、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> をページ モデル (*Pages/Index.cshtml.cs*) に挿入しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="7cf9b-333">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="7cf9b-334">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-334">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="7cf9b-336">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="7cf9b-337">サンプル アプリの例 5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="7cf9b-338"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="7cf9b-339">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="7cf9b-340">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="7cf9b-341">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="7cf9b-342">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="7cf9b-343">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="7cf9b-344">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="7cf9b-345">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="7cf9b-346">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="7cf9b-347">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-348">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="7cf9b-349">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="7cf9b-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="7cf9b-350">サンプル アプリの例 6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="7cf9b-351">名前付きオプションをサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="7cf9b-352">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="7cf9b-353">名前付きオプションでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-354">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-355">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="7cf9b-356">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-357">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="7cf9b-358">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="7cf9b-359">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="7cf9b-360">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="7cf9b-361">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="7cf9b-362">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="7cf9b-363">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="7cf9b-364">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="7cf9b-365">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-365">All options are named instances.</span></span> <span data-ttu-id="7cf9b-366">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7cf9b-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-368"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7cf9b-369">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7cf9b-370">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="7cf9b-370">OptionsBuilder API</span></span>

<span data-ttu-id="7cf9b-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-372">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7cf9b-373">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7cf9b-374">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-374">Use DI services to configure options</span></span>

<span data-ttu-id="7cf9b-375">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7cf9b-376">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7cf9b-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7cf9b-378"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7cf9b-379">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7cf9b-380">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7cf9b-381">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="7cf9b-382">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="7cf9b-382">Options validation</span></span>

<span data-ttu-id="7cf9b-383">オプションが構成されている場合は、オプションの検証を使用してオプションを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="7cf9b-384">オプションが有効なら `true` を、無効なら `false` を返す検証メソッドと共に、`Validate` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="7cf9b-385">前の例では、名前付きのオプションのインスタンスを `optionalOptionsName` に設定しました。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="7cf9b-386">既定のオプションのインスタンスは `Options.DefaultName` です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="7cf9b-387">オプションのインスタンスが作成されると、検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="7cf9b-388">オプションのインスタンスが最初にアクセスされる際は、検証に合格することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7cf9b-389">オプションの検証によって、オプションのインスタンスが作成された後のオプションの変更を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="7cf9b-390">たとえば、`IOptionsSnapshot` オプションは、オプションが最初にアクセスされたときに要求ごとに 1 回作成されて検証されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="7cf9b-391">`IOptionsSnapshot` オプションが、"*同じ要求の*" 後続のアクセス試行に対して検証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="7cf9b-392">`Validate` メソッドは、`Func<TOptions, bool>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="7cf9b-393">検証を完全にカスタマイズするには、`IValidateOptions<TOptions>` を実装します。これにより、次が可能になります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="7cf9b-394">複数のオプションの種類の検証: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="7cf9b-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="7cf9b-395">別のオプションの種類に基づく検証: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="7cf9b-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="7cf9b-396">`IValidateOptions` は次を検証します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="7cf9b-397">特定の名前付きのオプションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-397">A specific named options instance.</span></span>
* <span data-ttu-id="7cf9b-398">`name` が `null` の場合はすべてのオプション。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="7cf9b-399">インターフェイスの実装から `ValidateOptionsResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="7cf9b-400">データ注釈に基づく検証は、`OptionsBuilder<TOptions>` で <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> メソッドを呼び出すことにより、[Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) パッケージから利用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="7cf9b-401">`Microsoft.Extensions.Options.DataAnnotations` は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="7cf9b-402">先行検証 (起動時にフェイル ファストする) は今後のリリースでの導入が検討されています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="7cf9b-403">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-403">Options post-configuration</span></span>

<span data-ttu-id="7cf9b-404">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-405">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-407">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7cf9b-408">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-408">Accessing options during startup</span></span>

<span data-ttu-id="7cf9b-409">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7cf9b-410">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7cf9b-411">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="7cf9b-412">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7cf9b-413">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="7cf9b-414">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="7cf9b-415">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="7cf9b-416">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="7cf9b-417">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="7cf9b-418">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7cf9b-419">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7cf9b-419">Prerequisites</span></span>

<span data-ttu-id="7cf9b-420">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="7cf9b-421">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="7cf9b-421">Options interfaces</span></span>

<span data-ttu-id="7cf9b-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="7cf9b-424">変更通知</span><span class="sxs-lookup"><span data-stu-id="7cf9b-424">Change notifications</span></span>
* [<span data-ttu-id="7cf9b-425">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="7cf9b-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="7cf9b-426">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="7cf9b-427">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="7cf9b-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="7cf9b-428">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="7cf9b-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="7cf9b-430"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="7cf9b-431">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="7cf9b-432"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="7cf9b-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-434"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="7cf9b-435"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="7cf9b-436"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="7cf9b-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="7cf9b-438">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="7cf9b-439"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="7cf9b-440">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="7cf9b-441">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="7cf9b-442">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-442">General options configuration</span></span>

<span data-ttu-id="7cf9b-443">サンプル アプリの例 1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="7cf9b-444">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="7cf9b-445">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="7cf9b-446">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="7cf9b-447">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-448">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-449">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="7cf9b-450">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="7cf9b-451">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="7cf9b-452">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="7cf9b-453"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="7cf9b-454">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="7cf9b-455">サンプル アプリの例 2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="7cf9b-456">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-456">Use a delegate to set options values.</span></span> <span data-ttu-id="7cf9b-457">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="7cf9b-458">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-459">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7cf9b-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="7cf9b-461">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="7cf9b-462">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="7cf9b-463">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="7cf9b-464"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="7cf9b-465">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="7cf9b-466">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="7cf9b-467">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="7cf9b-468">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-468">Suboptions configuration</span></span>

<span data-ttu-id="7cf9b-469">サンプル アプリの例 3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="7cf9b-470">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="7cf9b-471">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="7cf9b-472">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="7cf9b-473">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="7cf9b-474">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="7cf9b-475">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-476">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7cf9b-477">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="7cf9b-478">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="7cf9b-479">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="7cf9b-480">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="7cf9b-481">ビュー モデルまたは直接的なビュー挿入で与えられるオプション</span><span class="sxs-lookup"><span data-stu-id="7cf9b-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="7cf9b-482">サンプル アプリの例 4 は、ビュー モデルまたは直接的なビュー挿入で与えられるオプションです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="7cf9b-483">オプションはビュー モデルで提供するか、ビューに <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を直接挿入することで提供できます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="7cf9b-484">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="7cf9b-485">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-485">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="7cf9b-487">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="7cf9b-488">サンプル アプリの例 5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="7cf9b-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> では、最小の処理オーバーヘッドでオプションを再読み込みできます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="7cf9b-490">オプションは、要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="7cf9b-491">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="7cf9b-492">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="7cf9b-493">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="7cf9b-494">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="7cf9b-495">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-496">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="7cf9b-497">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="7cf9b-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="7cf9b-498">サンプル アプリの例 6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="7cf9b-499">名前付きオプションをサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="7cf9b-500">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="7cf9b-501">名前付きオプションでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-502">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="7cf9b-503">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="7cf9b-504">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="7cf9b-505">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="7cf9b-506">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="7cf9b-507">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="7cf9b-508">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="7cf9b-509">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="7cf9b-510">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="7cf9b-511">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="7cf9b-512">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="7cf9b-513">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-513">All options are named instances.</span></span> <span data-ttu-id="7cf9b-514">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="7cf9b-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-516"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="7cf9b-517">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="7cf9b-518">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="7cf9b-518">OptionsBuilder API</span></span>

<span data-ttu-id="7cf9b-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="7cf9b-520">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="7cf9b-521">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="7cf9b-522">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="7cf9b-522">Use DI services to configure options</span></span>

<span data-ttu-id="7cf9b-523">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="7cf9b-524">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="7cf9b-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="7cf9b-526"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="7cf9b-527">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="7cf9b-528">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="7cf9b-529">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="7cf9b-530">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="7cf9b-530">Options post-configuration</span></span>

<span data-ttu-id="7cf9b-531">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="7cf9b-532">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="7cf9b-534">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="7cf9b-535">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="7cf9b-535">Accessing options during startup</span></span>

<span data-ttu-id="7cf9b-536">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="7cf9b-537">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7cf9b-538">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7cf9b-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7cf9b-539">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7cf9b-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
