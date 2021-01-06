---
title: ASP.NET Core のオプション パターン
author: rick-anderson
description: ASP.NET Core アプリの関連のある設定のグループを表すオプション パターンを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061094"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="9884b-103">ASP.NET Core のオプション パターン</span><span class="sxs-lookup"><span data-stu-id="9884b-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9884b-104">作成者: [Kirk Larkin](https://twitter.com/serpent5) および [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9884b-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="9884b-105">オプション パターンは、クラスを使用して、関連する設定のグループに、厳密に型指定されたアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9884b-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="9884b-106">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="9884b-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9884b-107">[インターフェイス分離の原則 (ISP) またはカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 使用する構成設定のみに依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="9884b-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9884b-108">[懸念事項の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): アプリのさまざまな部分の設定は、互いに依存していないか、結合されていない。</span><span class="sxs-lookup"><span data-stu-id="9884b-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9884b-109">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9884b-110">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9884b-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9884b-111">このトピックでは、ASP.NET Core でのオプションのパターンについて説明します。</span><span class="sxs-lookup"><span data-stu-id="9884b-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="9884b-112">コンソール アプリでオプションのパターンを使用する方法の詳細については、「[.NET でのオプションのパターン](/dotnet/core/extensions/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9884b-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="9884b-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9884b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="9884b-114">階層的な構成をバインドする</span><span class="sxs-lookup"><span data-stu-id="9884b-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="9884b-115">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="9884b-115">Options interfaces</span></span>

<span data-ttu-id="9884b-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="9884b-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="9884b-117">以下はサポート "\***されません** _": アプリが開始された後の構成データの読み取り。</span><span class="sxs-lookup"><span data-stu-id="9884b-117">Does \***not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="9884b-118">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="9884b-118">Named options</span></span>](#named)
* <span data-ttu-id="9884b-119">[シングルトン](xref:fundamentals/dependency-injection#singleton)として登録されており、任意の[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="9884b-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="9884b-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="9884b-121">要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9884b-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9884b-122">詳細については、「[IOptionsSnapshot を使用して更新データを読み取る](#ios)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9884b-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="9884b-123">[スコープ](xref:fundamentals/dependency-injection#scoped)として登録されているため、シングルトン サービスには挿入できません。</span><span class="sxs-lookup"><span data-stu-id="9884b-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="9884b-124">[名前付きオプション](#named)をサポートします。</span><span class="sxs-lookup"><span data-stu-id="9884b-124">Supports [named options](#named)</span></span>

<span data-ttu-id="9884b-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="9884b-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="9884b-126">オプションを取得し、`TOptions` インスタンスのオプション通知を管理するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="9884b-127">[シングルトン](xref:fundamentals/dependency-injection#singleton)として登録されており、任意の[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="9884b-128">サポートするものは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9884b-128">Supports:</span></span>
  * <span data-ttu-id="9884b-129">変更通知</span><span class="sxs-lookup"><span data-stu-id="9884b-129">Change notifications</span></span>
  * [<span data-ttu-id="9884b-130">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="9884b-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="9884b-131">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="9884b-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="9884b-132">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9884b-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="9884b-133">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9884b-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9884b-135"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9884b-136">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="9884b-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9884b-137"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9884b-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="9884b-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9884b-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="9884b-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9884b-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9884b-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="9884b-142">IOptionsSnapshot を使用して更新データを読み取る</span><span class="sxs-lookup"><span data-stu-id="9884b-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="9884b-143"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="9884b-144">更新された構成値の読み取りをサポートする構成プロバイダーを使用しているとき、構成の変更は、アプリの開始後に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9884b-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="9884b-145">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9884b-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="9884b-146">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="9884b-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="9884b-147">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="9884b-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="9884b-148">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="9884b-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="9884b-149">次のコードでは <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="9884b-150">次のコードは、`MyOptions` のバインド先となる構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9884b-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-151">上記のコードでは、アプリが開始された後の JSON 構成ファイルへの変更が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9884b-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="9884b-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="9884b-152">IOptionsMonitor</span></span>

<span data-ttu-id="9884b-153">次のコードは、`MyOptions` のバインド先となる構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9884b-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-154"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> の使用例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="9884b-155">上記のコードでは、アプリが開始された後の JSON 構成ファイルへの変更は既定で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9884b-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="9884b-156">IConfigureNamedOptions を使用した名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="9884b-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="9884b-157">名前付きオプション:</span><span class="sxs-lookup"><span data-stu-id="9884b-157">Named options:</span></span>

* <span data-ttu-id="9884b-158">複数の構成セクションが同じプロパティにバインドされている場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="9884b-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="9884b-159">大文字と小文字の区別があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-159">Are case sensitive.</span></span>

<span data-ttu-id="9884b-160">次の *appsettings.json* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9884b-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="9884b-161">`TopItem:Month` と `TopItem:Year` をバインドする 2 つのクラスを作成するのではなく、各セクションに対して次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="9884b-162">次のコードは、名前付きオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-163">次のコードは、名前付きオプションを表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="9884b-164">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="9884b-164">All options are named instances.</span></span> <span data-ttu-id="9884b-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9884b-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="9884b-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-167"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="9884b-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9884b-168">名前付きオプション `null` は、特定の名前付きインスタンスではなく、すべての名前付きインスタンスを対象とするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="9884b-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> では、この規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9884b-170">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="9884b-170">OptionsBuilder API</span></span>

<span data-ttu-id="9884b-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9884b-172">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="9884b-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9884b-173">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="9884b-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="9884b-174">`OptionsBuilder` は、「[オプションの検証](#val)」セクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9884b-175">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-175">Use DI services to configure options</span></span>

<span data-ttu-id="9884b-176">オプションの構成中、次の 2 つの方法で依存関係挿入からサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9884b-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9884b-177">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="9884b-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9884b-178">`OptionsBuilder<TOptions>` から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9884b-179"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="9884b-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9884b-180">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="9884b-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9884b-181">型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すときにフレームワークが行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="9884b-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9884b-182">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="9884b-183">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="9884b-183">Options validation</span></span>

<span data-ttu-id="9884b-184">オプションの検証により、オプションの値を検証できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="9884b-185">次の *appsettings.json* ファイルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9884b-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="9884b-186">次のクラスは、`"MyConfig"` 構成セクションにバインドされ、いくつかの `DataAnnotations` 規則を適用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="9884b-187">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-187">The following code:</span></span>

* <span data-ttu-id="9884b-188"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> を呼び出し、`MyConfigOptions` クラスにバインドされる [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) を取得します。</span><span class="sxs-lookup"><span data-stu-id="9884b-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="9884b-189"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> を呼び出し、`DataAnnotations` を利用した検証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9884b-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="9884b-190">`ValidateDataAnnotations` 拡張メソッドは [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet パッケージに定義されています。</span><span class="sxs-lookup"><span data-stu-id="9884b-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="9884b-191">`Microsoft.NET.Sdk.Web` SDK を使用する Web アプリの場合、このパッケージは共有フレームワークから暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="9884b-192">次のコードは、構成値または検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="9884b-193">次のコードは、デリゲートを使用して、より複雑な検証規則を適用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="9884b-194">複雑な検証用の IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="9884b-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="9884b-195">次のクラスは、<xref:Microsoft.Extensions.Options.IValidateOptions`1> を実装します。</span><span class="sxs-lookup"><span data-stu-id="9884b-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="9884b-196">`IValidateOptions` は、`StartUp` からクラスに検証コードを移動できるようにします。</span><span class="sxs-lookup"><span data-stu-id="9884b-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="9884b-197">前のコードを使用すると、次のコードにより `Startup.ConfigureServices` で検証が有効になります。</span><span class="sxs-lookup"><span data-stu-id="9884b-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="9884b-198">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="9884b-198">Options post-configuration</span></span>

<span data-ttu-id="9884b-199">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-200">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-202">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9884b-203">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="9884b-203">Accessing options during startup</span></span>

<span data-ttu-id="9884b-204">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9884b-205">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9884b-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9884b-206">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="9884b-207">Options.ConfigurationExtensions NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="9884b-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="9884b-208">ASP.NET Core アプリでは、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージが暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9884b-209">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9884b-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9884b-210">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="9884b-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9884b-211">[インターフェイス分離の原則 (ISP) またはカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 使用する構成設定のみに依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="9884b-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9884b-212">[懸念事項の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): アプリのさまざまな部分の設定は、互いに依存していないか、結合されていない。</span><span class="sxs-lookup"><span data-stu-id="9884b-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9884b-213">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9884b-214">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9884b-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9884b-215">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9884b-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9884b-216">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9884b-216">Prerequisites</span></span>

<span data-ttu-id="9884b-217">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="9884b-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="9884b-218">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="9884b-218">Options interfaces</span></span>

<span data-ttu-id="9884b-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="9884b-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="9884b-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="9884b-221">変更通知</span><span class="sxs-lookup"><span data-stu-id="9884b-221">Change notifications</span></span>
* [<span data-ttu-id="9884b-222">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="9884b-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="9884b-223">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="9884b-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="9884b-224">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9884b-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="9884b-225">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9884b-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9884b-227"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9884b-228">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="9884b-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9884b-229"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9884b-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="9884b-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9884b-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="9884b-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9884b-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9884b-233"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="9884b-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9884b-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9884b-235">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9884b-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="9884b-236"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="9884b-237">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="9884b-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="9884b-238">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="9884b-239">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="9884b-239">General options configuration</span></span>

<span data-ttu-id="9884b-240">サンプル アプリの例 1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="9884b-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="9884b-241">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="9884b-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9884b-242">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="9884b-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="9884b-243">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="9884b-244">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-244">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="9884b-245">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="9884b-246">次のページ モデルは [コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="9884b-247">サンプルの *appsettings.json* ファイルによって `option1` と `option2` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="9884b-248">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="9884b-249">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9884b-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="9884b-250"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9884b-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="9884b-251">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="9884b-252">サンプル アプリの例 2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9884b-253">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-253">Use a delegate to set options values.</span></span> <span data-ttu-id="9884b-254">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="9884b-254">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="9884b-255">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9884b-256">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-257">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9884b-257">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-258">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="9884b-259">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="9884b-260">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9884b-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9884b-261"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="9884b-262">先の例では、`Option1` と `Option2` の値は両方とも *appsettings.json* で指定されていますが、構成されているデリゲートによって `Option1` と `Option2` の値はオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="9884b-263">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが *優先* され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="9884b-264">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="9884b-265">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="9884b-265">Suboptions configuration</span></span>

<span data-ttu-id="9884b-266">サンプル アプリの例 3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="9884b-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="9884b-267">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="9884b-268">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="9884b-269">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9884b-270">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="9884b-271">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9884b-272">`MySubOptions` が *appsettings.json* ファイルのセクション `subsection` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="9884b-273">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="9884b-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9884b-274">サンプルの *appsettings.json* ファイルでは、`suboption1` と `suboption2` のキーで `subsection` メンバーが定義されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="9884b-275">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="9884b-276">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-276">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="9884b-277">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="9884b-278">オプションの挿入</span><span class="sxs-lookup"><span data-stu-id="9884b-278">Options injection</span></span>

<span data-ttu-id="9884b-279">オプションの挿入は、サンプル アプリの例 4 に示されています。</span><span class="sxs-lookup"><span data-stu-id="9884b-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="9884b-280"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を次に挿入します。</span><span class="sxs-lookup"><span data-stu-id="9884b-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="9884b-281">[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを持つ Razor ページまたは MVC ビュー。</span><span class="sxs-lookup"><span data-stu-id="9884b-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="9884b-282">ページまたはビュー モデル。</span><span class="sxs-lookup"><span data-stu-id="9884b-282">A page or view model.</span></span>

<span data-ttu-id="9884b-283">サンプル アプリの次の例では、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> をページ モデル (*Pages/Index.cshtml.cs*) に挿入しています。</span><span class="sxs-lookup"><span data-stu-id="9884b-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="9884b-284">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9884b-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="9884b-285">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-285">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="9884b-287">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="9884b-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="9884b-288">サンプル アプリの例 5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="9884b-289"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="9884b-290">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9884b-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="9884b-291">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="9884b-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="9884b-292">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="9884b-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="9884b-293">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="9884b-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="9884b-294">次の例は、 *appsettings.json* の変更後に新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を作成する方法を示しています (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="9884b-295">サーバーに複数の要求が届くと、ファイルが変更されて構成が再読み込みされるまで、 *appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="9884b-296">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="9884b-297">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="9884b-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="9884b-298">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9884b-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="9884b-299">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9884b-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="9884b-300">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="9884b-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="9884b-301">サンプル アプリの例 6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="9884b-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="9884b-302">名前付きオプションをサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="9884b-303">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) を使用して宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9884b-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="9884b-304">名前付きオプションでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="9884b-305">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="9884b-306">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="9884b-307">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="9884b-308">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="9884b-309">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="9884b-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="9884b-310">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="9884b-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="9884b-311">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="9884b-312">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="9884b-313">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="9884b-314">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="9884b-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="9884b-315">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="9884b-316">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="9884b-316">All options are named instances.</span></span> <span data-ttu-id="9884b-317">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9884b-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="9884b-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-319"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="9884b-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9884b-320">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="9884b-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9884b-321">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="9884b-321">OptionsBuilder API</span></span>

<span data-ttu-id="9884b-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9884b-323">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="9884b-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9884b-324">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="9884b-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9884b-325">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-325">Use DI services to configure options</span></span>

<span data-ttu-id="9884b-326">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9884b-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9884b-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="9884b-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9884b-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9884b-329"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="9884b-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9884b-330">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="9884b-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9884b-331">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="9884b-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9884b-332">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="9884b-333">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="9884b-333">Options validation</span></span>

<span data-ttu-id="9884b-334">オプションが構成されている場合は、オプションの検証を使用してオプションを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="9884b-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="9884b-335">オプションが有効なら `true` を、無効なら `false` を返す検証メソッドと共に、`Validate` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9884b-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="9884b-336">前の例では、名前付きのオプションのインスタンスを `optionalOptionsName` に設定しました。</span><span class="sxs-lookup"><span data-stu-id="9884b-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="9884b-337">既定のオプションのインスタンスは `Options.DefaultName` です。</span><span class="sxs-lookup"><span data-stu-id="9884b-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="9884b-338">オプションのインスタンスが作成されると、検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="9884b-339">オプションのインスタンスが最初にアクセスされる際は、検証に合格することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9884b-340">オプションの検証によって、オプションのインスタンスが作成された後のオプションの変更を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="9884b-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="9884b-341">たとえば、`IOptionsSnapshot` オプションは、オプションが最初にアクセスされたときに要求ごとに 1 回作成されて検証されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="9884b-342">`IOptionsSnapshot` オプションが、"*同じ要求の*" 後続のアクセス試行に対して検証されることはありません。</span><span class="sxs-lookup"><span data-stu-id="9884b-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="9884b-343">`Validate` メソッドは、`Func<TOptions, bool>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9884b-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="9884b-344">検証を完全にカスタマイズするには、`IValidateOptions<TOptions>` を実装します。これにより、次が可能になります。</span><span class="sxs-lookup"><span data-stu-id="9884b-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="9884b-345">複数のオプションの種類の検証: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="9884b-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="9884b-346">別のオプションの種類に基づく検証: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="9884b-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="9884b-347">`IValidateOptions` は次を検証します。</span><span class="sxs-lookup"><span data-stu-id="9884b-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="9884b-348">特定の名前付きのオプションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="9884b-348">A specific named options instance.</span></span>
* <span data-ttu-id="9884b-349">`name` が `null` の場合はすべてのオプション。</span><span class="sxs-lookup"><span data-stu-id="9884b-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="9884b-350">インターフェイスの実装から `ValidateOptionsResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="9884b-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="9884b-351">データ注釈に基づく検証は、`OptionsBuilder<TOptions>` で <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> メソッドを呼び出すことにより、[Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) パッケージから利用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="9884b-352">`Microsoft.Extensions.Options.DataAnnotations` は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="9884b-353">先行検証 (起動時にフェイル ファストする) は今後のリリースでの導入が検討されています。</span><span class="sxs-lookup"><span data-stu-id="9884b-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="9884b-354">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="9884b-354">Options post-configuration</span></span>

<span data-ttu-id="9884b-355">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-356">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-358">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9884b-359">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="9884b-359">Accessing options during startup</span></span>

<span data-ttu-id="9884b-360">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9884b-361">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9884b-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9884b-362">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="9884b-363">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9884b-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9884b-364">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="9884b-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="9884b-365">[インターフェイス分離の原則 (ISP) またはカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 使用する構成設定のみに依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="9884b-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="9884b-366">[懸念事項の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): アプリのさまざまな部分の設定は、互いに依存していないか、結合されていない。</span><span class="sxs-lookup"><span data-stu-id="9884b-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="9884b-367">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="9884b-368">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9884b-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="9884b-369">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9884b-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9884b-370">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9884b-370">Prerequisites</span></span>

<span data-ttu-id="9884b-371">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="9884b-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="9884b-372">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="9884b-372">Options interfaces</span></span>

<span data-ttu-id="9884b-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="9884b-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="9884b-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="9884b-375">変更通知</span><span class="sxs-lookup"><span data-stu-id="9884b-375">Change notifications</span></span>
* [<span data-ttu-id="9884b-376">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="9884b-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="9884b-377">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="9884b-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="9884b-378">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="9884b-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="9884b-379">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="9884b-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="9884b-381"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="9884b-382">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="9884b-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="9884b-383"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="9884b-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="9884b-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="9884b-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="9884b-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="9884b-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="9884b-387"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="9884b-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9884b-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="9884b-389">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9884b-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="9884b-390"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="9884b-391">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="9884b-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="9884b-392">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="9884b-393">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="9884b-393">General options configuration</span></span>

<span data-ttu-id="9884b-394">サンプル アプリの例 1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="9884b-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="9884b-395">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="9884b-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="9884b-396">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="9884b-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="9884b-397">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="9884b-398">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-398">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="9884b-399">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="9884b-400">次のページ モデルは [コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="9884b-401">サンプルの *appsettings.json* ファイルによって `option1` と `option2` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="9884b-402">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="9884b-403">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9884b-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="9884b-404"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9884b-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="9884b-405">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="9884b-406">サンプル アプリの例 2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9884b-407">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-407">Use a delegate to set options values.</span></span> <span data-ttu-id="9884b-408">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="9884b-408">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="9884b-409">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9884b-410">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-411">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9884b-411">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="9884b-412">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="9884b-413">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="9884b-414">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9884b-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9884b-415"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="9884b-416">先の例では、`Option1` と `Option2` の値は両方とも *appsettings.json* で指定されていますが、構成されているデリゲートによって `Option1` と `Option2` の値はオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="9884b-417">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが *優先* され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="9884b-418">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="9884b-419">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="9884b-419">Suboptions configuration</span></span>

<span data-ttu-id="9884b-420">サンプル アプリの例 3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="9884b-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="9884b-421">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="9884b-422">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="9884b-423">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="9884b-424">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="9884b-425">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9884b-426">`MySubOptions` が *appsettings.json* ファイルのセクション `subsection` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9884b-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="9884b-427">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="9884b-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="9884b-428">サンプルの *appsettings.json* ファイルでは、`suboption1` と `suboption2` のキーで `subsection` メンバーが定義されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="9884b-429">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="9884b-430">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-430">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="9884b-431">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="9884b-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="9884b-432">ビュー モデルまたは直接的なビュー挿入で与えられるオプション</span><span class="sxs-lookup"><span data-stu-id="9884b-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="9884b-433">サンプル アプリの例 4 は、ビュー モデルまたは直接的なビュー挿入で与えられるオプションです。</span><span class="sxs-lookup"><span data-stu-id="9884b-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="9884b-434">オプションはビュー モデルで提供するか、ビューに <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を直接挿入することで提供できます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="9884b-435">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9884b-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="9884b-436">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-436">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="9884b-438">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="9884b-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="9884b-439">サンプル アプリの例 5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="9884b-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> では、最小の処理オーバーヘッドでオプションを再読み込みできます。</span><span class="sxs-lookup"><span data-stu-id="9884b-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="9884b-441">オプションは、要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="9884b-442">次の例は、 *appsettings.json* の変更後に新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を作成する方法を示しています (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="9884b-443">サーバーに複数の要求が届くと、ファイルが変更されて構成が再読み込みされるまで、 *appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="9884b-444">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="9884b-445">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="9884b-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="9884b-446">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9884b-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="9884b-447">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9884b-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="9884b-448">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="9884b-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="9884b-449">サンプル アプリの例 6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="9884b-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="9884b-450">名前付きオプションをサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="9884b-451">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) を使用して宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9884b-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="9884b-452">名前付きオプションでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="9884b-453">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9884b-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="9884b-454">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="9884b-455">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9884b-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="9884b-456">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="9884b-457">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="9884b-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="9884b-458">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="9884b-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="9884b-459">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="9884b-460">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="9884b-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="9884b-461">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="9884b-462">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="9884b-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="9884b-463">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="9884b-464">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="9884b-464">All options are named instances.</span></span> <span data-ttu-id="9884b-465">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="9884b-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="9884b-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-467"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="9884b-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="9884b-468">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="9884b-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="9884b-469">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="9884b-469">OptionsBuilder API</span></span>

<span data-ttu-id="9884b-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="9884b-471">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="9884b-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="9884b-472">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="9884b-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="9884b-473">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="9884b-473">Use DI services to configure options</span></span>

<span data-ttu-id="9884b-474">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9884b-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="9884b-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="9884b-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="9884b-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="9884b-477"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="9884b-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="9884b-478">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="9884b-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="9884b-479">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="9884b-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="9884b-480">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9884b-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="9884b-481">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="9884b-481">Options post-configuration</span></span>

<span data-ttu-id="9884b-482">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="9884b-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="9884b-483">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9884b-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="9884b-485">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9884b-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="9884b-486">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="9884b-486">Accessing options during startup</span></span>

<span data-ttu-id="9884b-487">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9884b-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="9884b-488">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9884b-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9884b-489">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9884b-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9884b-490">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9884b-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
