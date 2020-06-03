---
<span data-ttu-id="fb01c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb01c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb01c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb01c-102">'Blazor'</span></span>
- <span data-ttu-id="fb01c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb01c-103">'Identity'</span></span>
- <span data-ttu-id="fb01c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb01c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb01c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb01c-105">'Razor'</span></span>
- <span data-ttu-id="fb01c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb01c-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="fb01c-107">ASP.NET Core で変更トークンを使用して変更を検出する</span><span class="sxs-lookup"><span data-stu-id="fb01c-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb01c-108">"*変更トークン*" は、状態の変更を追跡するために使用される汎用の低レベル構成ブロックです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="fb01c-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fb01c-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="fb01c-110">IChangeToken インターフェイス</span><span class="sxs-lookup"><span data-stu-id="fb01c-110">IChangeToken interface</span></span>

<span data-ttu-id="fb01c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> により、変更が発生したという通知が伝達されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="fb01c-112">`IChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="fb01c-113">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージは、ASP.NET Core アプリに暗黙的に提供されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="fb01c-114">`IChangeToken` に次の 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="fb01c-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> は、トークンによってコールバックがプロアクティブに生成されるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="fb01c-116">`ActiveChangedCallbacks` が `false` に設定されている場合、コールバックは呼び出されず、アプリは変更のために `HasChanged` をポーリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="fb01c-117">変更がまったく発生しない場合、または基になる変更リスナーが破棄されるか無効になっている場合、トークンがまったくキャンセルされない可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="fb01c-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> は、変更が発生したかどうかを示す値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="fb01c-119">`IChangeToken` インターフェイスには、[RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) というメソッドが含まれています。これを使うと、トークンが変更されたときに呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="fb01c-120">コールバックが呼び出される前に `HasChanged` を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="fb01c-121">ChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="fb01c-121">ChangeToken class</span></span>

<span data-ttu-id="fb01c-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> は、変更が発生したことの通知を伝達するために使用される静的クラスです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="fb01c-123">`ChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="fb01c-124">[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージは、ASP.NET Core アプリに暗黙的に提供されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="fb01c-125">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) メソッドは、トークンが変更されるたびに呼び出される `Action` を登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="fb01c-126">`Func<IChangeToken>` はトークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="fb01c-127">`Action` は、トークンが変更されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="fb01c-128">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) オーバーロードは、トークン コンシューマー `Action` に渡される追加の `TState` パラメーターを取ります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="fb01c-129">`OnChange` では <xref:System.IDisposable> が返されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="fb01c-130"><xref:System.IDisposable.Dispose*> を呼び出すと、トークンによるその後の変更のリッスンが停止され、トークンのリソースが解放されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="fb01c-131">ASP.NET Core での変更のトークンの使用例</span><span class="sxs-lookup"><span data-stu-id="fb01c-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="fb01c-132">変更トークンは、オブジェクトの変更を監視するために ASP.NET Core の主要な領域で使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="fb01c-133">ファイルへの変更を監視するために、<xref:Microsoft.Extensions.FileProviders.IFileProvider> の <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> メソッドでは、指定された監視対象のファイルまたはフォルダーの `IChangeToken` が作成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="fb01c-134">`IChangeToken` トークンをキャッシュ エントリに追加して、変更時にキャッシュの削除をトリガーすることができます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="fb01c-135">`TOptions` の変更のために、<xref:Microsoft.Extensions.Options.OptionsMonitor`1> の既定の実装である <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> には、1 つ以上の <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> インスタンスを受け取るオーバーロードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="fb01c-136">各インスタンスは、オプションの変更を追跡するために変更通知のコールバックを登録する `IChangeToken` を返します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="fb01c-137">構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-137">Monitor for configuration changes</span></span>

<span data-ttu-id="fb01c-138">既定では、ASP.NET Core テンプレートは、[JSON 構成ファイル](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*、*appsettings.Development.json*、および *appsettings.Production.json*) を使用して、アプリの構成設定を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="fb01c-139">これらのファイルは、`reloadOnChange` パラメーターを受け取る、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上の [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 拡張メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="fb01c-140">`reloadOnChange` は、ファイルの変更時に構成を再読み込みするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="fb01c-141">この設定は、<xref:Microsoft.Extensions.Hosting.Host> の便利なメソッド <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 内に現れます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="fb01c-142">ファイル ベースの構成は、<xref:Microsoft.Extensions.Configuration.FileConfigurationSource> によって表されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="fb01c-143">`FileConfigurationSource` ではファイルを監視するために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="fb01c-144">既定では、`IFileMonitor` は <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> によって提供されます。これは、<xref:System.IO.FileSystemWatcher> を使用して、構成ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="fb01c-145">サンプル アプリは、構成の変更を監視するための 2 つの実装を示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="fb01c-146">*appsettings* ファイルのいずれかが変更されると、ファイルを監視する両方の実装によってカスタム コードが実行されます。サンプル アプリによりコンソールにメッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="fb01c-147">構成ファイルの `FileSystemWatcher` は、1 つの構成ファイルの変更に対して複数のトークンのコールバックをトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="fb01c-148">複数のトークンのコールバックがトリガーされたときにカスタム コードが一度だけ実行されるようにするために、サンプルの実装ではファイル ハッシュがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="fb01c-149">サンプルでは、SHA1 ファイル ハッシュが使用されています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="fb01c-150">再試行は、指数バックオフを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="fb01c-151">再実行が提供されるのは、ファイルの新しいハッシュの計算を一時的に妨げるファイル ロックが発生する場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="fb01c-152">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="fb01c-153">シンプルなスタートアップ変更トークン</span><span class="sxs-lookup"><span data-stu-id="fb01c-153">Simple startup change token</span></span>

<span data-ttu-id="fb01c-154">構成再読み込みトークンへの変更通知のために、トークン コンシューマー `Action` のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="fb01c-155">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="fb01c-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="fb01c-156">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="fb01c-157">コールバックは、`InvokeChanged` メソッドです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="fb01c-158">コールバックの `state` は、`IWebHostEnvironment` を渡すためのに使われます。これは監視する適切な *appsettings* 構成ファイルを指定するのに便利です (たとえば、開発環境の場合は *appsettings.Development.json*)。</span><span class="sxs-lookup"><span data-stu-id="fb01c-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="fb01c-159">ファイル ハッシュは、構成ファイルが 1 回のみ変更されたときの複数のトークンのコールバックのために `WriteConsole` ステートメントが複数回実行されるのを防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="fb01c-160">このシステムは、アプリが実行されている限り実行され、ユーザーが無効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="fb01c-161">サービスとしての構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="fb01c-162">サンプルは次のものを実装します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-162">The sample implements:</span></span>

* <span data-ttu-id="fb01c-163">基本的なスタートアップ トークンの監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="fb01c-164">サービスとしての監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-164">Monitoring as a service.</span></span>
* <span data-ttu-id="fb01c-165">監視を有効および無効にするメカニズム。</span><span class="sxs-lookup"><span data-stu-id="fb01c-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="fb01c-166">サンプルでは `IConfigurationMonitor` インターフェイスが設定されています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="fb01c-167">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="fb01c-168">実装済みのクラスのコンストラクター `ConfigurationMonitor` は、変更通知のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="fb01c-169">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="fb01c-170">`InvokeChanged` はコールバック メソッドです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="fb01c-171">このインスタンスの `state` は、監視の状態にアクセスするために使用される `IConfigurationMonitor` インスタンスへの参照です。</span><span class="sxs-lookup"><span data-stu-id="fb01c-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="fb01c-172">次の 2 つのプロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-172">Two properties are used:</span></span>

* <span data-ttu-id="fb01c-173">`MonitoringEnabled`:コールバックでそのカスタム コードを実行する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="fb01c-174">`CurrentState`:UI で使用するための現在の監視状態を説明します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="fb01c-175">`InvokeChanged` メソッドは、次の点を除けば、前の方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="fb01c-176">`MonitoringEnabled` が `true` ではない限りコードを実行しません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="fb01c-177">現在の `state` をその `WriteConsole` 出力に出力します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="fb01c-178">インスタンス `ConfigurationMonitor` は、`Startup.ConfigureServices` でサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="fb01c-179">インデックス ページは、ユーザーに構成監視の制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="fb01c-180">`IConfigurationMonitor` のインスタンスは、`IndexModel` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="fb01c-181">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="fb01c-182">構成監視 (`_monitor`) は、監視を有効または無効にし、UI フィードバックの現在の状態を設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="fb01c-183">`OnPostStartMonitoring` がトリガーされると、監視が有効になり、現在の状態がクリアされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="fb01c-184">`OnPostStopMonitoring` がトリガーされると、監視が無効になり、監視が発生していないことを反映するように状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="fb01c-185">UI のボタンを使って監視を有効および無効にできます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="fb01c-186">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="fb01c-187">キャッシュされたファイルの変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-187">Monitor cached file changes</span></span>

<span data-ttu-id="fb01c-188"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> を使用して、ファイルの内容をメモリ内にキャッシュすることができます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="fb01c-189">メモリ内キャッシュの説明については、「[メモリ内キャッシュ](xref:performance/caching/memory)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fb01c-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="fb01c-190">以下に示す実装など、追加の手順を実行しないと、ソース データが変更された場合に*期限切れの* (古い) データがキャッシュから返されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="fb01c-191">たとえば、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)期間の更新時にキャッシュされたソース ファイルの状態を考慮しないと、キャッシュされたファイル データが古くなります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="fb01c-192">データの各要求が、スライド式有効期限を更新しますが、ファイルがキャッシュに再読み込みされることはありません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="fb01c-193">ファイルのキャッシュされた内容を使用するアプリの機能は、古い内容を受け取る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="fb01c-194">ファイル キャッシュのシナリオで変更トークンを使用すると、キャッシュに古いファイルの内容が残ることを防止できます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="fb01c-195">サンプル アプリは、このアプローチの実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="fb01c-196">このサンプルでは、`GetFileContent` を使用して次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="fb01c-197">ファイルの内容を返します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-197">Return file content.</span></span>
* <span data-ttu-id="fb01c-198">指数バックオフを使用する再試行アルゴリズムを実装し、ファイル ロックによりファイルの読み取りが一時的に妨げられるケースに対処します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="fb01c-199">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="fb01c-200">`FileService` はキャッシュされたファイルの参照の処理するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="fb01c-201">サービスの `GetFileContent` メソッドの呼び出しは、メモリ内キャッシュからファイルの内容を取得して、呼び出し元 (*Services/FileService.cs*) に戻そうとします。</span><span class="sxs-lookup"><span data-stu-id="fb01c-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="fb01c-202">キャッシュ キーを使用してキャッシュされた内容が見つからない場合、次の操作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="fb01c-203">`GetFileContent` を使用してファイルの内容が取得されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="fb01c-204">変更トークンは、[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) を使用してファイル プロバイダーから取得します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="fb01c-205">ファイルが変更されたときに、トークンのコールバックがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="fb01c-206">ファイルの内容は、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)の期間キャシュされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="fb01c-207">ファイルがキャッシュされている間に変更された場合、キャッシュ エントリを削除するために、[MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) を使用して変更トークンがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="fb01c-208">次の例では、ファイルはアプリの[コンテンツ ルート](xref:fundamentals/index#content-root)に格納されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="fb01c-209">`IWebHostEnvironment.ContentRootFileProvider` は、アプリの `IWebHostEnvironment.ContentRootPath` を指す <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="fb01c-210">[IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) を使って `filePath` を取得します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="fb01c-211">`FileService` はメモリ キャッシュ サービスと共にサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="fb01c-212">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="fb01c-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="fb01c-213">ページ モデルでは、このサービスを使ってファイルの内容が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="fb01c-214">Index ページの `OnGet` メソッド内 (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="fb01c-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="fb01c-215">CompositeChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="fb01c-215">CompositeChangeToken class</span></span>

<span data-ttu-id="fb01c-216">1 つのオブジェクト内で 1 つまたは複数の `IChangeToken` インスタンスを表すためには、<xref:Microsoft.Extensions.Primitives.CompositeChangeToken> クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="fb01c-217">複合トークンの `HasChanged` は、いずれかの表現されているトークン `HasChanged` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="fb01c-218">複合トークンの `ActiveChangeCallbacks` は、いずれかの表現されているトークン `ActiveChangeCallbacks` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="fb01c-219">複数の同時変更イベントが発生すると、複合変更コールバックが 1 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb01c-220">"*変更トークン*" は、状態の変更を追跡するために使用される汎用の低レベル構成ブロックです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="fb01c-221">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fb01c-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="fb01c-222">IChangeToken インターフェイス</span><span class="sxs-lookup"><span data-stu-id="fb01c-222">IChangeToken interface</span></span>

<span data-ttu-id="fb01c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> により、変更が発生したという通知が伝達されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="fb01c-224">`IChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="fb01c-225">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を使用しないアプリの場合は、[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージのパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="fb01c-226">`IChangeToken` に次の 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="fb01c-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> は、トークンによってコールバックがプロアクティブに生成されるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="fb01c-228">`ActiveChangedCallbacks` が `false` に設定されている場合、コールバックは呼び出されず、アプリは変更のために `HasChanged` をポーリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="fb01c-229">変更がまったく発生しない場合、または基になる変更リスナーが破棄されるか無効になっている場合、トークンがまったくキャンセルされない可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="fb01c-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> は、変更が発生したかどうかを示す値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="fb01c-231">`IChangeToken` インターフェイスには、[RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) というメソッドが含まれています。これを使うと、トークンが変更されたときに呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="fb01c-232">コールバックが呼び出される前に `HasChanged` を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="fb01c-233">ChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="fb01c-233">ChangeToken class</span></span>

<span data-ttu-id="fb01c-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> は、変更が発生したことの通知を伝達するために使用される静的クラスです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="fb01c-235">`ChangeToken` は <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="fb01c-236">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を使用しないアプリの場合は、[Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet パッケージのパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="fb01c-237">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) メソッドは、トークンが変更されるたびに呼び出される `Action` を登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="fb01c-238">`Func<IChangeToken>` はトークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="fb01c-239">`Action` は、トークンが変更されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="fb01c-240">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) オーバーロードは、トークン コンシューマー `Action` に渡される追加の `TState` パラメーターを取ります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="fb01c-241">`OnChange` では <xref:System.IDisposable> が返されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="fb01c-242"><xref:System.IDisposable.Dispose*> を呼び出すと、トークンによるその後の変更のリッスンが停止され、トークンのリソースが解放されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="fb01c-243">ASP.NET Core での変更のトークンの使用例</span><span class="sxs-lookup"><span data-stu-id="fb01c-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="fb01c-244">変更トークンは、オブジェクトの変更を監視するために ASP.NET Core の主要な領域で使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="fb01c-245">ファイルへの変更を監視するために、<xref:Microsoft.Extensions.FileProviders.IFileProvider> の <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> メソッドでは、指定された監視対象のファイルまたはフォルダーの `IChangeToken` が作成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="fb01c-246">`IChangeToken` トークンをキャッシュ エントリに追加して、変更時にキャッシュの削除をトリガーすることができます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="fb01c-247">`TOptions` の変更のために、<xref:Microsoft.Extensions.Options.OptionsMonitor`1> の既定の実装である <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> には、1 つ以上の <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> インスタンスを受け取るオーバーロードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="fb01c-248">各インスタンスは、オプションの変更を追跡するために変更通知のコールバックを登録する `IChangeToken` を返します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="fb01c-249">構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-249">Monitor for configuration changes</span></span>

<span data-ttu-id="fb01c-250">既定では、ASP.NET Core テンプレートは、[JSON 構成ファイル](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*、*appsettings.Development.json*、および *appsettings.Production.json*) を使用して、アプリの構成設定を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="fb01c-251">これらのファイルは、`reloadOnChange` パラメーターを受け取る、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上の [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 拡張メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="fb01c-252">`reloadOnChange` は、ファイルの変更時に構成を再読み込みするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="fb01c-253">この設定は、<xref:Microsoft.AspNetCore.WebHost> の便利なメソッド <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 内に現れます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="fb01c-254">ファイル ベースの構成は、<xref:Microsoft.Extensions.Configuration.FileConfigurationSource> によって表されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="fb01c-255">`FileConfigurationSource` ではファイルを監視するために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="fb01c-256">既定では、`IFileMonitor` は <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> によって提供されます。これは、<xref:System.IO.FileSystemWatcher> を使用して、構成ファイルの変更を監視します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="fb01c-257">サンプル アプリは、構成の変更を監視するための 2 つの実装を示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="fb01c-258">*appsettings* ファイルのいずれかが変更されると、ファイルを監視する両方の実装によってカスタム コードが実行されます。サンプル アプリによりコンソールにメッセージが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="fb01c-259">構成ファイルの `FileSystemWatcher` は、1 つの構成ファイルの変更に対して複数のトークンのコールバックをトリガーできます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="fb01c-260">複数のトークンのコールバックがトリガーされたときにカスタム コードが一度だけ実行されるようにするために、サンプルの実装ではファイル ハッシュがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="fb01c-261">サンプルでは、SHA1 ファイル ハッシュが使用されています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="fb01c-262">再試行は、指数バックオフを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="fb01c-263">再実行が提供されるのは、ファイルの新しいハッシュの計算を一時的に妨げるファイル ロックが発生する場合があるためです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="fb01c-264">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="fb01c-265">シンプルなスタートアップ変更トークン</span><span class="sxs-lookup"><span data-stu-id="fb01c-265">Simple startup change token</span></span>

<span data-ttu-id="fb01c-266">構成再読み込みトークンへの変更通知のために、トークン コンシューマー `Action` のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="fb01c-267">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="fb01c-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="fb01c-268">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="fb01c-269">コールバックは、`InvokeChanged` メソッドです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="fb01c-270">コールバックの `state` は、`IHostingEnvironment` を渡すためのに使われます。これは監視する適切な *appsettings* 構成ファイルを指定するのに便利です (たとえば、開発環境の場合は *appsettings.Development.json*)。</span><span class="sxs-lookup"><span data-stu-id="fb01c-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="fb01c-271">ファイル ハッシュは、構成ファイルが 1 回のみ変更されたときの複数のトークンのコールバックのために `WriteConsole` ステートメントが複数回実行されるのを防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="fb01c-272">このシステムは、アプリが実行されている限り実行され、ユーザーが無効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="fb01c-273">サービスとしての構成の変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="fb01c-274">サンプルは次のものを実装します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-274">The sample implements:</span></span>

* <span data-ttu-id="fb01c-275">基本的なスタートアップ トークンの監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="fb01c-276">サービスとしての監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-276">Monitoring as a service.</span></span>
* <span data-ttu-id="fb01c-277">監視を有効および無効にするメカニズム。</span><span class="sxs-lookup"><span data-stu-id="fb01c-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="fb01c-278">サンプルでは `IConfigurationMonitor` インターフェイスが設定されています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="fb01c-279">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="fb01c-280">実装済みのクラスのコンストラクター `ConfigurationMonitor` は、変更通知のコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="fb01c-281">`config.GetReloadToken()` はトークンを提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="fb01c-282">`InvokeChanged` はコールバック メソッドです。</span><span class="sxs-lookup"><span data-stu-id="fb01c-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="fb01c-283">このインスタンスの `state` は、監視の状態にアクセスするために使用される `IConfigurationMonitor` インスタンスへの参照です。</span><span class="sxs-lookup"><span data-stu-id="fb01c-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="fb01c-284">次の 2 つのプロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-284">Two properties are used:</span></span>

* <span data-ttu-id="fb01c-285">`MonitoringEnabled`:コールバックでそのカスタム コードを実行する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="fb01c-286">`CurrentState`:UI で使用するための現在の監視状態を説明します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="fb01c-287">`InvokeChanged` メソッドは、次の点を除けば、前の方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="fb01c-288">`MonitoringEnabled` が `true` ではない限りコードを実行しません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="fb01c-289">現在の `state` をその `WriteConsole` 出力に出力します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="fb01c-290">インスタンス `ConfigurationMonitor` は、`Startup.ConfigureServices` でサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="fb01c-291">インデックス ページは、ユーザーに構成監視の制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="fb01c-292">`IConfigurationMonitor` のインスタンスは、`IndexModel` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="fb01c-293">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="fb01c-294">構成監視 (`_monitor`) は、監視を有効または無効にし、UI フィードバックの現在の状態を設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="fb01c-295">`OnPostStartMonitoring` がトリガーされると、監視が有効になり、現在の状態がクリアされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="fb01c-296">`OnPostStopMonitoring` がトリガーされると、監視が無効になり、監視が発生していないことを反映するように状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="fb01c-297">UI のボタンを使って監視を有効および無効にできます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="fb01c-298">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="fb01c-299">キャッシュされたファイルの変更の監視</span><span class="sxs-lookup"><span data-stu-id="fb01c-299">Monitor cached file changes</span></span>

<span data-ttu-id="fb01c-300"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> を使用して、ファイルの内容をメモリ内にキャッシュすることができます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="fb01c-301">メモリ内キャッシュの説明については、「[メモリ内キャッシュ](xref:performance/caching/memory)」トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fb01c-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="fb01c-302">以下に示す実装など、追加の手順を実行しないと、ソース データが変更された場合に*期限切れの* (古い) データがキャッシュから返されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="fb01c-303">たとえば、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)期間の更新時にキャッシュされたソース ファイルの状態を考慮しないと、キャッシュされたファイル データが古くなります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="fb01c-304">データの各要求が、スライド式有効期限を更新しますが、ファイルがキャッシュに再読み込みされることはありません。</span><span class="sxs-lookup"><span data-stu-id="fb01c-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="fb01c-305">ファイルのキャッシュされた内容を使用するアプリの機能は、古い内容を受け取る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fb01c-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="fb01c-306">ファイル キャッシュのシナリオで変更トークンを使用すると、キャッシュに古いファイルの内容が残ることを防止できます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="fb01c-307">サンプル アプリは、このアプローチの実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="fb01c-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="fb01c-308">このサンプルでは、`GetFileContent` を使用して次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="fb01c-309">ファイルの内容を返します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-309">Return file content.</span></span>
* <span data-ttu-id="fb01c-310">指数バックオフを使用する再試行アルゴリズムを実装し、ファイル ロックによりファイルの読み取りが一時的に妨げられるケースに対処します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="fb01c-311">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb01c-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="fb01c-312">`FileService` はキャッシュされたファイルの参照の処理するために作成されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="fb01c-313">サービスの `GetFileContent` メソッドの呼び出しは、メモリ内キャッシュからファイルの内容を取得して、呼び出し元 (*Services/FileService.cs*) に戻そうとします。</span><span class="sxs-lookup"><span data-stu-id="fb01c-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="fb01c-314">キャッシュ キーを使用してキャッシュされた内容が見つからない場合、次の操作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="fb01c-315">`GetFileContent` を使用してファイルの内容が取得されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="fb01c-316">変更トークンは、[IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) を使用してファイル プロバイダーから取得します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="fb01c-317">ファイルが変更されたときに、トークンのコールバックがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="fb01c-318">ファイルの内容は、[スライド式有効期限](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)の期間キャシュされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="fb01c-319">ファイルがキャッシュされている間に変更された場合、キャッシュ エントリを削除するために、[MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) を使用して変更トークンがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="fb01c-320">次の例では、ファイルはアプリの[コンテンツ ルート](xref:fundamentals/index#content-root)に格納されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="fb01c-321">アプリの <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> を指す <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得するために、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) が使われます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="fb01c-322">[IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) を使って `filePath` を取得します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="fb01c-323">`FileService` はメモリ キャッシュ サービスと共にサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="fb01c-324">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="fb01c-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="fb01c-325">ページ モデルでは、このサービスを使ってファイルの内容が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="fb01c-326">Index ページの `OnGet` メソッド内 (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="fb01c-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="fb01c-327">CompositeChangeToken クラス</span><span class="sxs-lookup"><span data-stu-id="fb01c-327">CompositeChangeToken class</span></span>

<span data-ttu-id="fb01c-328">1 つのオブジェクト内で 1 つまたは複数の `IChangeToken` インスタンスを表すためには、<xref:Microsoft.Extensions.Primitives.CompositeChangeToken> クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="fb01c-329">複合トークンの `HasChanged` は、いずれかの表現されているトークン `HasChanged` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="fb01c-330">複合トークンの `ActiveChangeCallbacks` は、いずれかの表現されているトークン `ActiveChangeCallbacks` が `true` である場合に、`true` を報告します。</span><span class="sxs-lookup"><span data-stu-id="fb01c-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="fb01c-331">複数の同時変更イベントが発生すると、複合変更コールバックが 1 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fb01c-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fb01c-332">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fb01c-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
