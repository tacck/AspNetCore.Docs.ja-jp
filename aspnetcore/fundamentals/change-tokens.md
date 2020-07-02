---
title: ASP.NET Core で変更トークンを使用して変更を検出する
author: rick-anderson
description: 変更トークンを使用して変更を追跡する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: dd271f9db016c35eb072749258a499168b39010f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408436"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="8234d-103">ASP.NET Core で変更トークンを使用して変更を検出する</span><span class="sxs-lookup"><span data-stu-id="8234d-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8234d-104">"*変更トークン*" は、状態の変更を追跡するために使用される汎用の低レベル構成ブロックです。</span><span class="sxs-lookup"><span data-stu-id="8234d-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="8234d-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8234d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="8234d-106">IChangeToken インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8234d-106">IChangeToken interface</span></span>

<span data-ttu-id="8234d-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> により、変更が発生したという通知が伝達されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="8234d-108">`IChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="8234d-109">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージは、ASP.NET Core アプリに暗黙的に提供されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="8234d-110">`IChangeToken` に次の 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="8234d-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> は、トークンによってコールバックがプロアクティブに生成されるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="8234d-112">`ActiveChangedCallbacks` が `false` に設定されている場合、コールバックは呼び出されず、アプリは変更のために `HasChanged` をポーリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="8234d-113">変更がまったく発生しない場合、または基になる変更リスナーが破棄されるか無効になっている場合、トークンがまったくキャンセルされない可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="8234d-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> は、変更が発生したかどうかを示す値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8234d-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="8234d-115">`IChangeToken` インターフェイスには、[RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) というメソッドが含まれています。これを使うと、トークンが変更されたときに呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="8234d-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="8234d-116">コールバックが呼び出される前に `HasChanged` を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="8234d-117">ChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="8234d-117">ChangeToken class</span></span>

<span data-ttu-id="8234d-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> は、変更が発生したことの通知を伝達するために使用される静的クラスです。</span><span class="sxs-lookup"><span data-stu-id="8234d-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="8234d-119">`ChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="8234d-120">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージは、ASP.NET Core アプリに暗黙的に提供されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="8234d-121">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) メソッドは、トークンが変更されるたびに呼び出される `Action` を登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="8234d-122">`Func<IChangeToken>` はトークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="8234d-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="8234d-123">`Action` は、トークンが変更されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="8234d-124">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) オーバーロードは、トークン コンシューマー `Action` に渡される追加の `TState` パラメーターを取ります。</span><span class="sxs-lookup"><span data-stu-id="8234d-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="8234d-125">`OnChange` では <xref:System.IDisposable> が返されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="8234d-126"><xref:System.IDisposable.Dispose*> を呼び出すと、トークンによるその後の変更のリッスンが停止され、トークンのリソースが解放されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="8234d-127">ASP.NET Core での変更のトークンの使用例</span><span class="sxs-lookup"><span data-stu-id="8234d-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="8234d-128">変更トークンは、オブジェクトの変更を監視するために ASP.NET Core の主要な領域で使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="8234d-129">ファイルへの変更を監視するために、<xref:Microsoft.Extensions.FileProviders.IFileProvider> の <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> メソッドでは、指定された監視対象のファイルまたはフォルダーの `IChangeToken` が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="8234d-130">`IChangeToken` トークンをキャッシュ エントリに追加して、変更時にキャッシュの削除をトリガーすることができます。</span><span class="sxs-lookup"><span data-stu-id="8234d-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="8234d-131">`TOptions` の変更のために、<xref:Microsoft.Extensions.Options.OptionsMonitor`1> の既定の実装である <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> には、1 つ以上の <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> インスタンスを受け取るオーバーロードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8234d-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="8234d-132">各インスタンスは、オプションの変更を追跡するために変更通知のコールバックを登録する `IChangeToken` を返します。</span><span class="sxs-lookup"><span data-stu-id="8234d-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="8234d-133">構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-133">Monitor for configuration changes</span></span>

<span data-ttu-id="8234d-134">既定では、ASP.NET Core テンプレートは、[JSON 構成ファイル](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*、*appsettings.Development.json*、および *appsettings.Production.json*) を使用して、アプリの構成設定を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8234d-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="8234d-135">これらのファイルは、`reloadOnChange` パラメーターを受け取る、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上の [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 拡張メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="8234d-136">`reloadOnChange` は、ファイルの変更時に構成を再読み込みするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="8234d-137">この設定は、<xref:Microsoft.Extensions.Hosting.Host> の便利なメソッド <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 内に現れます。</span><span class="sxs-lookup"><span data-stu-id="8234d-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="8234d-138">ファイル ベースの構成は、<xref:Microsoft.Extensions.Configuration.FileConfigurationSource> によって表されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="8234d-139">`FileConfigurationSource` ではファイルを監視するために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="8234d-140">既定では、`IFileMonitor` は <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> によって提供されます。これは、<xref:System.IO.FileSystemWatcher> を使用して、構成ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="8234d-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="8234d-141">サンプル アプリは、構成の変更を監視するための 2 つの実装を示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="8234d-142">*appsettings* ファイルのいずれかが変更されると、ファイルを監視する両方の実装によってカスタム コードが実行されます。サンプル アプリによりコンソールにメッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8234d-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="8234d-143">構成ファイルの `FileSystemWatcher` は、1 つの構成ファイルの変更に対して複数のトークンのコールバックをトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="8234d-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="8234d-144">複数のトークンのコールバックがトリガーされたときにカスタム コードが一度だけ実行されるようにするために、サンプルの実装ではファイル ハッシュがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="8234d-145">サンプルでは、SHA1 ファイル ハッシュが使用されています。</span><span class="sxs-lookup"><span data-stu-id="8234d-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="8234d-146">再試行は、指数バックオフを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="8234d-147">再実行が提供されるのは、ファイルの新しいハッシュの計算を一時的に妨げるファイル ロックが発生する場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="8234d-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="8234d-148">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="8234d-149">シンプルなスタートアップ変更トークン</span><span class="sxs-lookup"><span data-stu-id="8234d-149">Simple startup change token</span></span>

<span data-ttu-id="8234d-150">構成再読み込みトークンへの変更通知のために、トークン コンシューマー `Action` のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="8234d-151">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="8234d-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="8234d-152">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="8234d-153">コールバックは、`InvokeChanged` メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8234d-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="8234d-154">コールバックの `state` は、`IWebHostEnvironment` を渡すためのに使われます。これは監視する適切な *appsettings* 構成ファイルを指定するのに便利です (たとえば、開発環境の場合は *appsettings.Development.json*)。</span><span class="sxs-lookup"><span data-stu-id="8234d-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="8234d-155">ファイル ハッシュは、構成ファイルが 1 回のみ変更されたときの複数のトークンのコールバックのために `WriteConsole` ステートメントが複数回実行されるのを防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="8234d-156">このシステムは、アプリが実行されている限り実行され、ユーザーが無効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8234d-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="8234d-157">サービスとしての構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="8234d-158">サンプルは次のものを実装します。</span><span class="sxs-lookup"><span data-stu-id="8234d-158">The sample implements:</span></span>

* <span data-ttu-id="8234d-159">基本的なスタートアップ トークンの監視</span><span class="sxs-lookup"><span data-stu-id="8234d-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="8234d-160">サービスとしての監視</span><span class="sxs-lookup"><span data-stu-id="8234d-160">Monitoring as a service.</span></span>
* <span data-ttu-id="8234d-161">監視を有効および無効にするメカニズム。</span><span class="sxs-lookup"><span data-stu-id="8234d-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="8234d-162">サンプルでは `IConfigurationMonitor` インターフェイスが設定されています。</span><span class="sxs-lookup"><span data-stu-id="8234d-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="8234d-163">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="8234d-164">実装済みのクラスのコンストラクター `ConfigurationMonitor` は、変更通知のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="8234d-165">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="8234d-166">`InvokeChanged` はコールバック メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8234d-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="8234d-167">このインスタンスの `state` は、監視の状態にアクセスするために使用される `IConfigurationMonitor` インスタンスへの参照です。</span><span class="sxs-lookup"><span data-stu-id="8234d-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="8234d-168">次の 2 つのプロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-168">Two properties are used:</span></span>

* <span data-ttu-id="8234d-169">`MonitoringEnabled`:コールバックでそのカスタム コードを実行する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="8234d-170">`CurrentState`:UI で使用するための現在の監視状態を説明します。</span><span class="sxs-lookup"><span data-stu-id="8234d-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="8234d-171">`InvokeChanged` メソッドは、次の点を除けば、前の方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="8234d-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="8234d-172">`MonitoringEnabled` が `true` ではない限りコードを実行しません。</span><span class="sxs-lookup"><span data-stu-id="8234d-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="8234d-173">現在の `state` をその `WriteConsole` 出力に出力します。</span><span class="sxs-lookup"><span data-stu-id="8234d-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="8234d-174">インスタンス `ConfigurationMonitor` は、`Startup.ConfigureServices` でサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="8234d-175">インデックス ページは、ユーザーに構成監視の制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="8234d-176">`IConfigurationMonitor` のインスタンスは、`IndexModel` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="8234d-177">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="8234d-178">構成監視 (`_monitor`) は、監視を有効または無効にし、UI フィードバックの現在の状態を設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="8234d-179">`OnPostStartMonitoring` がトリガーされると、監視が有効になり、現在の状態がクリアされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="8234d-180">`OnPostStopMonitoring` がトリガーされると、監視が無効になり、監視が発生していないことを反映するように状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="8234d-181">UI のボタンを使って監視を有効および無効にできます。</span><span class="sxs-lookup"><span data-stu-id="8234d-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="8234d-182">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8234d-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="8234d-183">キャッシュされたファイルの変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-183">Monitor cached file changes</span></span>

<span data-ttu-id="8234d-184"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> を使用して、ファイルの内容をメモリ内にキャッシュすることができます。</span><span class="sxs-lookup"><span data-stu-id="8234d-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="8234d-185">メモリ内キャッシュの説明については、「[メモリ内キャッシュ](xref:performance/caching/memory)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8234d-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="8234d-186">以下に示す実装など、追加の手順を実行しないと、ソース データが変更された場合に*期限切れの* (古い) データがキャッシュから返されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="8234d-187">たとえば、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)期間の更新時にキャッシュされたソース ファイルの状態を考慮しないと、キャッシュされたファイル データが古くなります。</span><span class="sxs-lookup"><span data-stu-id="8234d-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="8234d-188">データの各要求が、スライド式有効期限を更新しますが、ファイルがキャッシュに再読み込みされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8234d-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="8234d-189">ファイルのキャッシュされた内容を使用するアプリの機能は、古い内容を受け取る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="8234d-190">ファイル キャッシュのシナリオで変更トークンを使用すると、キャッシュに古いファイルの内容が残ることを防止できます。</span><span class="sxs-lookup"><span data-stu-id="8234d-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="8234d-191">サンプル アプリは、このアプローチの実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="8234d-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="8234d-192">このサンプルでは、`GetFileContent` を使用して次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="8234d-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="8234d-193">ファイルの内容を返します。</span><span class="sxs-lookup"><span data-stu-id="8234d-193">Return file content.</span></span>
* <span data-ttu-id="8234d-194">指数バックオフを使用する再試行アルゴリズムを実装し、ファイル ロックによりファイルの読み取りが一時的に妨げられるケースに対処します。</span><span class="sxs-lookup"><span data-stu-id="8234d-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="8234d-195">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="8234d-196">`FileService` はキャッシュされたファイルの参照の処理するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="8234d-197">サービスの `GetFileContent` メソッドの呼び出しは、メモリ内キャッシュからファイルの内容を取得して、呼び出し元 (*Services/FileService.cs*) に戻そうとします。</span><span class="sxs-lookup"><span data-stu-id="8234d-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="8234d-198">キャッシュ キーを使用してキャッシュされた内容が見つからない場合、次の操作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="8234d-199">`GetFileContent` を使用してファイルの内容が取得されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="8234d-200">変更トークンは、[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) を使用してファイル プロバイダーから取得します。</span><span class="sxs-lookup"><span data-stu-id="8234d-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="8234d-201">ファイルが変更されたときに、トークンのコールバックがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="8234d-202">ファイルの内容は、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)の期間キャシュされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="8234d-203">ファイルがキャッシュされている間に変更された場合、キャッシュ エントリを削除するために、[MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) を使用して変更トークンがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="8234d-204">次の例では、ファイルはアプリの[コンテンツ ルート](xref:fundamentals/index#content-root)に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="8234d-205">`IWebHostEnvironment.ContentRootFileProvider` は、アプリの `IWebHostEnvironment.ContentRootPath` を指す <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="8234d-206">[IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) を使って `filePath` を取得します。</span><span class="sxs-lookup"><span data-stu-id="8234d-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="8234d-207">`FileService` はメモリ キャッシュ サービスと共にサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="8234d-208">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="8234d-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="8234d-209">ページ モデルでは、このサービスを使ってファイルの内容が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8234d-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="8234d-210">Index ページの `OnGet` メソッド内 (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8234d-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="8234d-211">CompositeChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="8234d-211">CompositeChangeToken class</span></span>

<span data-ttu-id="8234d-212">1 つのオブジェクト内で 1 つまたは複数の `IChangeToken` インスタンスを表すためには、<xref:Microsoft.Extensions.Primitives.CompositeChangeToken> クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8234d-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="8234d-213">複合トークンの `HasChanged` は、いずれかの表現されているトークン `HasChanged` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="8234d-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="8234d-214">複合トークンの `ActiveChangeCallbacks` は、いずれかの表現されているトークン `ActiveChangeCallbacks` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="8234d-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="8234d-215">複数の同時変更イベントが発生すると、複合変更コールバックが 1 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8234d-216">"*変更トークン*" は、状態の変更を追跡するために使用される汎用の低レベル構成ブロックです。</span><span class="sxs-lookup"><span data-stu-id="8234d-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="8234d-217">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8234d-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="8234d-218">IChangeToken インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8234d-218">IChangeToken interface</span></span>

<span data-ttu-id="8234d-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> により、変更が発生したという通知が伝達されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="8234d-220">`IChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="8234d-221">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を使用しないアプリの場合は、[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージのパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="8234d-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="8234d-222">`IChangeToken` に次の 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="8234d-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> は、トークンによってコールバックがプロアクティブに生成されるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="8234d-224">`ActiveChangedCallbacks` が `false` に設定されている場合、コールバックは呼び出されず、アプリは変更のために `HasChanged` をポーリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="8234d-225">変更がまったく発生しない場合、または基になる変更リスナーが破棄されるか無効になっている場合、トークンがまったくキャンセルされない可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="8234d-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> は、変更が発生したかどうかを示す値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8234d-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="8234d-227">`IChangeToken` インターフェイスには、[RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) というメソッドが含まれています。これを使うと、トークンが変更されたときに呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="8234d-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="8234d-228">コールバックが呼び出される前に `HasChanged` を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="8234d-229">ChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="8234d-229">ChangeToken class</span></span>

<span data-ttu-id="8234d-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> は、変更が発生したことの通知を伝達するために使用される静的クラスです。</span><span class="sxs-lookup"><span data-stu-id="8234d-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="8234d-231">`ChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="8234d-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="8234d-232">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を使用しないアプリの場合は、[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージのパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="8234d-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="8234d-233">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) メソッドは、トークンが変更されるたびに呼び出される `Action` を登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="8234d-234">`Func<IChangeToken>` はトークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="8234d-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="8234d-235">`Action` は、トークンが変更されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="8234d-236">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) オーバーロードは、トークン コンシューマー `Action` に渡される追加の `TState` パラメーターを取ります。</span><span class="sxs-lookup"><span data-stu-id="8234d-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="8234d-237">`OnChange` では <xref:System.IDisposable> が返されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="8234d-238"><xref:System.IDisposable.Dispose*> を呼び出すと、トークンによるその後の変更のリッスンが停止され、トークンのリソースが解放されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="8234d-239">ASP.NET Core での変更のトークンの使用例</span><span class="sxs-lookup"><span data-stu-id="8234d-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="8234d-240">変更トークンは、オブジェクトの変更を監視するために ASP.NET Core の主要な領域で使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="8234d-241">ファイルへの変更を監視するために、<xref:Microsoft.Extensions.FileProviders.IFileProvider> の <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> メソッドでは、指定された監視対象のファイルまたはフォルダーの `IChangeToken` が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="8234d-242">`IChangeToken` トークンをキャッシュ エントリに追加して、変更時にキャッシュの削除をトリガーすることができます。</span><span class="sxs-lookup"><span data-stu-id="8234d-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="8234d-243">`TOptions` の変更のために、<xref:Microsoft.Extensions.Options.OptionsMonitor`1> の既定の実装である <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> には、1 つ以上の <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> インスタンスを受け取るオーバーロードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8234d-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="8234d-244">各インスタンスは、オプションの変更を追跡するために変更通知のコールバックを登録する `IChangeToken` を返します。</span><span class="sxs-lookup"><span data-stu-id="8234d-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="8234d-245">構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-245">Monitor for configuration changes</span></span>

<span data-ttu-id="8234d-246">既定では、ASP.NET Core テンプレートは、[JSON 構成ファイル](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*、*appsettings.Development.json*、および *appsettings.Production.json*) を使用して、アプリの構成設定を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8234d-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="8234d-247">これらのファイルは、`reloadOnChange` パラメーターを受け取る、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上の [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 拡張メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="8234d-248">`reloadOnChange` は、ファイルの変更時に構成を再読み込みするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="8234d-249">この設定は、<xref:Microsoft.AspNetCore.WebHost> の便利なメソッド <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 内に現れます。</span><span class="sxs-lookup"><span data-stu-id="8234d-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="8234d-250">ファイル ベースの構成は、<xref:Microsoft.Extensions.Configuration.FileConfigurationSource> によって表されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="8234d-251">`FileConfigurationSource` ではファイルを監視するために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="8234d-252">既定では、`IFileMonitor` は <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> によって提供されます。これは、<xref:System.IO.FileSystemWatcher> を使用して、構成ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="8234d-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="8234d-253">サンプル アプリは、構成の変更を監視するための 2 つの実装を示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="8234d-254">*appsettings* ファイルのいずれかが変更されると、ファイルを監視する両方の実装によってカスタム コードが実行されます。サンプル アプリによりコンソールにメッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8234d-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="8234d-255">構成ファイルの `FileSystemWatcher` は、1 つの構成ファイルの変更に対して複数のトークンのコールバックをトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="8234d-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="8234d-256">複数のトークンのコールバックがトリガーされたときにカスタム コードが一度だけ実行されるようにするために、サンプルの実装ではファイル ハッシュがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="8234d-257">サンプルでは、SHA1 ファイル ハッシュが使用されています。</span><span class="sxs-lookup"><span data-stu-id="8234d-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="8234d-258">再試行は、指数バックオフを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="8234d-259">再実行が提供されるのは、ファイルの新しいハッシュの計算を一時的に妨げるファイル ロックが発生する場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="8234d-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="8234d-260">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="8234d-261">シンプルなスタートアップ変更トークン</span><span class="sxs-lookup"><span data-stu-id="8234d-261">Simple startup change token</span></span>

<span data-ttu-id="8234d-262">構成再読み込みトークンへの変更通知のために、トークン コンシューマー `Action` のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="8234d-263">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="8234d-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="8234d-264">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="8234d-265">コールバックは、`InvokeChanged` メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8234d-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="8234d-266">コールバックの `state` は、`IHostingEnvironment` を渡すためのに使われます。これは監視する適切な *appsettings* 構成ファイルを指定するのに便利です (たとえば、開発環境の場合は *appsettings.Development.json*)。</span><span class="sxs-lookup"><span data-stu-id="8234d-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="8234d-267">ファイル ハッシュは、構成ファイルが 1 回のみ変更されたときの複数のトークンのコールバックのために `WriteConsole` ステートメントが複数回実行されるのを防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="8234d-268">このシステムは、アプリが実行されている限り実行され、ユーザーが無効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8234d-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="8234d-269">サービスとしての構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="8234d-270">サンプルは次のものを実装します。</span><span class="sxs-lookup"><span data-stu-id="8234d-270">The sample implements:</span></span>

* <span data-ttu-id="8234d-271">基本的なスタートアップ トークンの監視</span><span class="sxs-lookup"><span data-stu-id="8234d-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="8234d-272">サービスとしての監視</span><span class="sxs-lookup"><span data-stu-id="8234d-272">Monitoring as a service.</span></span>
* <span data-ttu-id="8234d-273">監視を有効および無効にするメカニズム。</span><span class="sxs-lookup"><span data-stu-id="8234d-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="8234d-274">サンプルでは `IConfigurationMonitor` インターフェイスが設定されています。</span><span class="sxs-lookup"><span data-stu-id="8234d-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="8234d-275">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="8234d-276">実装済みのクラスのコンストラクター `ConfigurationMonitor` は、変更通知のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="8234d-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="8234d-277">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="8234d-278">`InvokeChanged` はコールバック メソッドです。</span><span class="sxs-lookup"><span data-stu-id="8234d-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="8234d-279">このインスタンスの `state` は、監視の状態にアクセスするために使用される `IConfigurationMonitor` インスタンスへの参照です。</span><span class="sxs-lookup"><span data-stu-id="8234d-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="8234d-280">次の 2 つのプロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-280">Two properties are used:</span></span>

* <span data-ttu-id="8234d-281">`MonitoringEnabled`:コールバックでそのカスタム コードを実行する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8234d-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="8234d-282">`CurrentState`:UI で使用するための現在の監視状態を説明します。</span><span class="sxs-lookup"><span data-stu-id="8234d-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="8234d-283">`InvokeChanged` メソッドは、次の点を除けば、前の方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="8234d-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="8234d-284">`MonitoringEnabled` が `true` ではない限りコードを実行しません。</span><span class="sxs-lookup"><span data-stu-id="8234d-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="8234d-285">現在の `state` をその `WriteConsole` 出力に出力します。</span><span class="sxs-lookup"><span data-stu-id="8234d-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="8234d-286">インスタンス `ConfigurationMonitor` は、`Startup.ConfigureServices` でサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="8234d-287">インデックス ページは、ユーザーに構成監視の制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="8234d-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="8234d-288">`IConfigurationMonitor` のインスタンスは、`IndexModel` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="8234d-289">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="8234d-290">構成監視 (`_monitor`) は、監視を有効または無効にし、UI フィードバックの現在の状態を設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="8234d-291">`OnPostStartMonitoring` がトリガーされると、監視が有効になり、現在の状態がクリアされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="8234d-292">`OnPostStopMonitoring` がトリガーされると、監視が無効になり、監視が発生していないことを反映するように状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="8234d-293">UI のボタンを使って監視を有効および無効にできます。</span><span class="sxs-lookup"><span data-stu-id="8234d-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="8234d-294">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8234d-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="8234d-295">キャッシュされたファイルの変更の監視</span><span class="sxs-lookup"><span data-stu-id="8234d-295">Monitor cached file changes</span></span>

<span data-ttu-id="8234d-296"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> を使用して、ファイルの内容をメモリ内にキャッシュすることができます。</span><span class="sxs-lookup"><span data-stu-id="8234d-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="8234d-297">メモリ内キャッシュの説明については、「[メモリ内キャッシュ](xref:performance/caching/memory)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8234d-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="8234d-298">以下に示す実装など、追加の手順を実行しないと、ソース データが変更された場合に*期限切れの* (古い) データがキャッシュから返されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="8234d-299">たとえば、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)期間の更新時にキャッシュされたソース ファイルの状態を考慮しないと、キャッシュされたファイル データが古くなります。</span><span class="sxs-lookup"><span data-stu-id="8234d-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="8234d-300">データの各要求が、スライド式有効期限を更新しますが、ファイルがキャッシュに再読み込みされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8234d-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="8234d-301">ファイルのキャッシュされた内容を使用するアプリの機能は、古い内容を受け取る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8234d-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="8234d-302">ファイル キャッシュのシナリオで変更トークンを使用すると、キャッシュに古いファイルの内容が残ることを防止できます。</span><span class="sxs-lookup"><span data-stu-id="8234d-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="8234d-303">サンプル アプリは、このアプローチの実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="8234d-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="8234d-304">このサンプルでは、`GetFileContent` を使用して次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="8234d-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="8234d-305">ファイルの内容を返します。</span><span class="sxs-lookup"><span data-stu-id="8234d-305">Return file content.</span></span>
* <span data-ttu-id="8234d-306">指数バックオフを使用する再試行アルゴリズムを実装し、ファイル ロックによりファイルの読み取りが一時的に妨げられるケースに対処します。</span><span class="sxs-lookup"><span data-stu-id="8234d-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="8234d-307">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="8234d-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="8234d-308">`FileService` はキャッシュされたファイルの参照の処理するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="8234d-309">サービスの `GetFileContent` メソッドの呼び出しは、メモリ内キャッシュからファイルの内容を取得して、呼び出し元 (*Services/FileService.cs*) に戻そうとします。</span><span class="sxs-lookup"><span data-stu-id="8234d-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="8234d-310">キャッシュ キーを使用してキャッシュされた内容が見つからない場合、次の操作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="8234d-311">`GetFileContent` を使用してファイルの内容が取得されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="8234d-312">変更トークンは、[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) を使用してファイル プロバイダーから取得します。</span><span class="sxs-lookup"><span data-stu-id="8234d-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="8234d-313">ファイルが変更されたときに、トークンのコールバックがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="8234d-314">ファイルの内容は、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)の期間キャシュされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="8234d-315">ファイルがキャッシュされている間に変更された場合、キャッシュ エントリを削除するために、[MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) を使用して変更トークンがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="8234d-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="8234d-316">次の例では、ファイルはアプリの[コンテンツ ルート](xref:fundamentals/index#content-root)に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="8234d-317">アプリの <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> を指す <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得するために、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) が使われます。</span><span class="sxs-lookup"><span data-stu-id="8234d-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="8234d-318">[IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) を使って `filePath` を取得します。</span><span class="sxs-lookup"><span data-stu-id="8234d-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="8234d-319">`FileService` はメモリ キャッシュ サービスと共にサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="8234d-320">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="8234d-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="8234d-321">ページ モデルでは、このサービスを使ってファイルの内容が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8234d-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="8234d-322">Index ページの `OnGet` メソッド内 (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8234d-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="8234d-323">CompositeChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="8234d-323">CompositeChangeToken class</span></span>

<span data-ttu-id="8234d-324">1 つのオブジェクト内で 1 つまたは複数の `IChangeToken` インスタンスを表すためには、<xref:Microsoft.Extensions.Primitives.CompositeChangeToken> クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8234d-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="8234d-325">複合トークンの `HasChanged` は、いずれかの表現されているトークン `HasChanged` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="8234d-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="8234d-326">複合トークンの `ActiveChangeCallbacks` は、いずれかの表現されているトークン `ActiveChangeCallbacks` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="8234d-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="8234d-327">複数の同時変更イベントが発生すると、複合変更コールバックが 1 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8234d-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8234d-328">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8234d-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
