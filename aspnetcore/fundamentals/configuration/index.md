---
<span data-ttu-id="03889-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-102">'Blazor'</span></span>
- <span data-ttu-id="03889-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-103">'Identity'</span></span>
- <span data-ttu-id="03889-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-105">'Razor'</span></span>
- <span data-ttu-id="03889-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="03889-107">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="03889-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="03889-108">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="03889-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03889-109">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="03889-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="03889-110">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="03889-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="03889-111">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="03889-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="03889-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="03889-112">Environment variables</span></span>
* <span data-ttu-id="03889-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="03889-113">Azure Key Vault</span></span>
* <span data-ttu-id="03889-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="03889-114">Azure App Configuration</span></span>
* <span data-ttu-id="03889-115">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="03889-115">Command-line arguments</span></span>
* <span data-ttu-id="03889-116">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="03889-117">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="03889-117">Directory files</span></span>
* <span data-ttu-id="03889-118">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="03889-118">In-memory .NET objects</span></span>

<span data-ttu-id="03889-119">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="03889-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="03889-120">既定の構成</span><span class="sxs-lookup"><span data-stu-id="03889-120">Default configuration</span></span>

<span data-ttu-id="03889-121">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="03889-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="03889-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="03889-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="03889-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="03889-124">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="03889-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="03889-125">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="03889-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="03889-126">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="03889-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="03889-127">たとえば、*appsettings*.***Production***.*json* および  *appsettings*.***Development***.*json*。</span><span class="sxs-lookup"><span data-stu-id="03889-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="03889-128">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="03889-129">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="03889-130">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="03889-131">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="03889-132">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="03889-133">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#command-line-configuration-provider) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="03889-134">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="03889-135">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="03889-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="03889-136">appsettings.json</span></span>

<span data-ttu-id="03889-137">以下の *appsettings.json* ファイルについて考えます:</span><span class="sxs-lookup"><span data-stu-id="03889-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="03889-138">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-139">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="03889-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="03889-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="03889-140">*appsettings.json*</span></span>
1. <span data-ttu-id="03889-141">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production***.*json* および *appsettings*.***Development***.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="03889-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="03889-142">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="03889-143">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="03889-144">*appsettings*.`Environment`.*json* の値は、*appsettings. json*のキーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="03889-145">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="03889-145">For example, by default:</span></span>

* <span data-ttu-id="03889-146">開発においては、*appsettings*.***Development***.*json* 構成が *appsettings.json* の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="03889-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="03889-147">運用環境では、*appsettings*.***Production***.*json* 構成が *appsettings. json*の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="03889-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="03889-148">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="03889-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="03889-149">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="03889-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="03889-150">[既定の](#default)構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効化できます。</span><span class="sxs-lookup"><span data-stu-id="03889-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="03889-151">アプリの***開始後***に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="03889-152">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="03889-153">セキュリティとシークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="03889-153">Security and secret manager</span></span>

<span data-ttu-id="03889-154">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="03889-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="03889-155">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="03889-156">[シークレット マネージャー](xref:security/app-secrets) を使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="03889-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="03889-157">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="03889-158">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="03889-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="03889-159">[既定](#default)では、[シークレット マネージャー](xref:security/app-secrets)は*appsettings.json* と *appsettings.* `Environment` *.json* の後に構成設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="03889-160">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="03889-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="03889-161"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="03889-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="03889-162">シークレット マネージャーでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="03889-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="03889-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="03889-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="03889-164">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="03889-165">環境変数</span><span class="sxs-lookup"><span data-stu-id="03889-165">Environment variables</span></span>

<span data-ttu-id="03889-166">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> は *appsettings.json*、*appsettings.* `Environment` *.json*、および [シークレット マネージャー](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="03889-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="03889-167">そのため、環境から読み取られたキー値は、*appsettings.json*、*appsettings.* `Environment` *.json*、シークレット マネージャーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="03889-168">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="03889-168">The following `set` commands:</span></span>

* <span data-ttu-id="03889-169">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="03889-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="03889-170">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="03889-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="03889-171">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="03889-172">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="03889-172">The preceding environment settings:</span></span>

* <span data-ttu-id="03889-173">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="03889-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="03889-174">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="03889-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="03889-175">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="03889-176">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="03889-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="03889-177">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="03889-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="03889-178">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="03889-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="03889-179">上記のコマンドが *appsettings.json* と *appsettings.* `Environment` *.json* をオーバーライドすることをテストするには:</span><span class="sxs-lookup"><span data-stu-id="03889-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="03889-180">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="03889-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="03889-181">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="03889-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="03889-182">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="03889-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="03889-183">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="03889-183">In the preceding code:</span></span>

* <span data-ttu-id="03889-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="03889-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="03889-185">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="03889-186">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="03889-187">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="03889-188">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="03889-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="03889-189">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="03889-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="03889-190">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="03889-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="03889-191">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="03889-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="03889-192">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="03889-193">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。</span><span class="sxs-lookup"><span data-stu-id="03889-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="03889-194">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="03889-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="03889-195">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="03889-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="03889-196">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="03889-196">Exposed as environment variables.</span></span>

<span data-ttu-id="03889-197">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="03889-198">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="03889-199">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="03889-199">Command-line</span></span>

<span data-ttu-id="03889-200">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="03889-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="03889-201">*appsettings.json* と *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="03889-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="03889-202">開発環境の [App シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03889-203">環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-203">Environment variables.</span></span>

<span data-ttu-id="03889-204">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="03889-205">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="03889-205">Command-line arguments</span></span>

<span data-ttu-id="03889-206">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="03889-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="03889-207">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="03889-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="03889-208">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="03889-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="03889-209">キーの値:</span><span class="sxs-lookup"><span data-stu-id="03889-209">The key value:</span></span>

* <span data-ttu-id="03889-210">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="03889-211">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="03889-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="03889-212">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="03889-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="03889-213">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="03889-214">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="03889-214">Switch mappings</span></span>

<span data-ttu-id="03889-215">スイッチ マッピングでは、**キー**名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="03889-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="03889-216"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="03889-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="03889-217">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="03889-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="03889-218">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="03889-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="03889-219">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="03889-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="03889-220">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="03889-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="03889-221">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="03889-222">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="03889-223">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="03889-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="03889-224">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="03889-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-225">次のコマンドを実行して、キーの置換をテストします：</span><span class="sxs-lookup"><span data-stu-id="03889-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="03889-226">メモ:現時点では、`=` を使用してシングルダッシュの `-` でキーの置換値を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="03889-227">[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/3059)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="03889-228">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="03889-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="03889-229">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="03889-230">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03889-231">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="03889-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="03889-232">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="03889-232">Hierarchical configuration data</span></span>

<span data-ttu-id="03889-233">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="03889-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="03889-234">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *appsettings.json* 　ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="03889-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="03889-235">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="03889-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-236">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="03889-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="03889-237">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="03889-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="03889-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="03889-239">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="03889-240">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="03889-240">Configuration keys and values</span></span>

<span data-ttu-id="03889-241">構成キー:</span><span class="sxs-lookup"><span data-stu-id="03889-241">Configuration keys:</span></span>

* <span data-ttu-id="03889-242">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="03889-242">Are case-insensitive.</span></span> <span data-ttu-id="03889-243">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="03889-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="03889-244">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="03889-245">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="03889-246">階層キー</span><span class="sxs-lookup"><span data-stu-id="03889-246">Hierarchical keys</span></span>
  * <span data-ttu-id="03889-247">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="03889-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="03889-248">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="03889-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="03889-249">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="03889-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="03889-250">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="03889-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="03889-251">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="03889-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="03889-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="03889-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03889-253">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="03889-254">構成値:</span><span class="sxs-lookup"><span data-stu-id="03889-254">Configuration values:</span></span>

* <span data-ttu-id="03889-255">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="03889-255">Are strings.</span></span>
* <span data-ttu-id="03889-256">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="03889-257">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-257">Configuration providers</span></span>

<span data-ttu-id="03889-258">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="03889-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="03889-259">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-259">Provider</span></span> | <span data-ttu-id="03889-260">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="03889-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="03889-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-262">'Blazor'</span></span>
- <span data-ttu-id="03889-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-263">'Identity'</span></span>
- <span data-ttu-id="03889-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-265">'Razor'</span></span>
- <span data-ttu-id="03889-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-268">'Blazor'</span></span>
- <span data-ttu-id="03889-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-269">'Identity'</span></span>
- <span data-ttu-id="03889-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-271">'Razor'</span></span>
- <span data-ttu-id="03889-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-272">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-274">'Blazor'</span></span>
- <span data-ttu-id="03889-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-275">'Identity'</span></span>
- <span data-ttu-id="03889-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-277">'Razor'</span></span>
- <span data-ttu-id="03889-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-280">'Blazor'</span></span>
- <span data-ttu-id="03889-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-281">'Identity'</span></span>
- <span data-ttu-id="03889-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-283">'Razor'</span></span>
- <span data-ttu-id="03889-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-286">'Blazor'</span></span>
- <span data-ttu-id="03889-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-287">'Identity'</span></span>
- <span data-ttu-id="03889-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-289">'Razor'</span></span>
- <span data-ttu-id="03889-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-292">'Blazor'</span></span>
- <span data-ttu-id="03889-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-293">'Identity'</span></span>
- <span data-ttu-id="03889-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-295">'Razor'</span></span>
- <span data-ttu-id="03889-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-298">'Blazor'</span></span>
- <span data-ttu-id="03889-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-299">'Identity'</span></span>
- <span data-ttu-id="03889-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-301">'Razor'</span></span>
- <span data-ttu-id="03889-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-304">'Blazor'</span></span>
- <span data-ttu-id="03889-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-305">'Identity'</span></span>
- <span data-ttu-id="03889-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-307">'Razor'</span></span>
- <span data-ttu-id="03889-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-310">'Blazor'</span></span>
- <span data-ttu-id="03889-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-311">'Identity'</span></span>
- <span data-ttu-id="03889-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-313">'Razor'</span></span>
- <span data-ttu-id="03889-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-316">'Blazor'</span></span>
- <span data-ttu-id="03889-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-317">'Identity'</span></span>
- <span data-ttu-id="03889-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-319">'Razor'</span></span>
- <span data-ttu-id="03889-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-322">'Blazor'</span></span>
- <span data-ttu-id="03889-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-323">'Identity'</span></span>
- <span data-ttu-id="03889-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-325">'Razor'</span></span>
- <span data-ttu-id="03889-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-328">'Blazor'</span></span>
- <span data-ttu-id="03889-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-329">'Identity'</span></span>
- <span data-ttu-id="03889-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-331">'Razor'</span></span>
- <span data-ttu-id="03889-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-334">'Blazor'</span></span>
- <span data-ttu-id="03889-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-335">'Identity'</span></span>
- <span data-ttu-id="03889-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-337">'Razor'</span></span>
- <span data-ttu-id="03889-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-340">'Blazor'</span></span>
- <span data-ttu-id="03889-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-341">'Identity'</span></span>
- <span data-ttu-id="03889-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-343">'Razor'</span></span>
- <span data-ttu-id="03889-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-346">'Blazor'</span></span>
- <span data-ttu-id="03889-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-347">'Identity'</span></span>
- <span data-ttu-id="03889-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-349">'Razor'</span></span>
- <span data-ttu-id="03889-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-352">'Blazor'</span></span>
- <span data-ttu-id="03889-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-353">'Identity'</span></span>
- <span data-ttu-id="03889-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-355">'Razor'</span></span>
- <span data-ttu-id="03889-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-358">'Blazor'</span></span>
- <span data-ttu-id="03889-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-359">'Identity'</span></span>
- <span data-ttu-id="03889-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-361">'Razor'</span></span>
- <span data-ttu-id="03889-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-362">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-363">------------------ | | [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure アプリ構成プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [コマンドライン構成プロバイダー](#clcp) | コマンドライン パラメーター | | [カスタム構成プロバイダー](#custom-configuration-provider) | カスタム ソース | | [環境変数構成プロバイダー](#evcp) | 環境変数 | | [ファイル構成プロバイダー](#file-configuration-provider) | INI、JSON、XML ファイル | | [ファイルごとのキーの構成プロバイダー](#key-per-file-configuration-provider) | ディレクトリ ファイル | | [メモリ構成プロバイダー](#memory-configuration-provider) | メモリ内コレクション | | [シークレット マネージャー](xref:security/app-secrets)  | ユーザー プロファイル ディレクトリのファイル |</span><span class="sxs-lookup"><span data-stu-id="03889-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="03889-364">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="03889-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="03889-365">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="03889-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="03889-366">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="03889-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="03889-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="03889-367">*appsettings.json*</span></span>
1. <span data-ttu-id="03889-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="03889-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="03889-369">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="03889-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="03889-370">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="03889-371">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="03889-372">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="03889-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="03889-373">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="03889-374">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="03889-374">Connection string prefixes</span></span>

<span data-ttu-id="03889-375">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="03889-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="03889-376">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="03889-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="03889-377">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="03889-378">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="03889-378">Connection string prefix</span></span> | <span data-ttu-id="03889-379">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-379">Provider</span></span> |
| ---
<span data-ttu-id="03889-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-381">'Blazor'</span></span>
- <span data-ttu-id="03889-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-382">'Identity'</span></span>
- <span data-ttu-id="03889-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-384">'Razor'</span></span>
- <span data-ttu-id="03889-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-387">'Blazor'</span></span>
- <span data-ttu-id="03889-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-388">'Identity'</span></span>
- <span data-ttu-id="03889-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-390">'Razor'</span></span>
- <span data-ttu-id="03889-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-393">'Blazor'</span></span>
- <span data-ttu-id="03889-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-394">'Identity'</span></span>
- <span data-ttu-id="03889-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-396">'Razor'</span></span>
- <span data-ttu-id="03889-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-399">'Blazor'</span></span>
- <span data-ttu-id="03889-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-400">'Identity'</span></span>
- <span data-ttu-id="03889-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-402">'Razor'</span></span>
- <span data-ttu-id="03889-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-405">'Blazor'</span></span>
- <span data-ttu-id="03889-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-406">'Identity'</span></span>
- <span data-ttu-id="03889-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-408">'Razor'</span></span>
- <span data-ttu-id="03889-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-411">'Blazor'</span></span>
- <span data-ttu-id="03889-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-412">'Identity'</span></span>
- <span data-ttu-id="03889-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-414">'Razor'</span></span>
- <span data-ttu-id="03889-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-417">'Blazor'</span></span>
- <span data-ttu-id="03889-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-418">'Identity'</span></span>
- <span data-ttu-id="03889-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-420">'Razor'</span></span>
- <span data-ttu-id="03889-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-423">'Blazor'</span></span>
- <span data-ttu-id="03889-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-424">'Identity'</span></span>
- <span data-ttu-id="03889-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-426">'Razor'</span></span>
- <span data-ttu-id="03889-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-429">'Blazor'</span></span>
- <span data-ttu-id="03889-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-430">'Identity'</span></span>
- <span data-ttu-id="03889-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-432">'Razor'</span></span>
- <span data-ttu-id="03889-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-435">'Blazor'</span></span>
- <span data-ttu-id="03889-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-436">'Identity'</span></span>
- <span data-ttu-id="03889-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-438">'Razor'</span></span>
- <span data-ttu-id="03889-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-439">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-441">'Blazor'</span></span>
- <span data-ttu-id="03889-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-442">'Identity'</span></span>
- <span data-ttu-id="03889-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-444">'Razor'</span></span>
- <span data-ttu-id="03889-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-447">'Blazor'</span></span>
- <span data-ttu-id="03889-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-448">'Identity'</span></span>
- <span data-ttu-id="03889-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-450">'Razor'</span></span>
- <span data-ttu-id="03889-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-451">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-452">---- | | `CUSTOMCONNSTR_` | カスタム プロバイダー | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="03889-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="03889-453">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="03889-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="03889-454">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="03889-455">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="03889-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="03889-456">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="03889-456">Environment variable key</span></span> | <span data-ttu-id="03889-457">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="03889-457">Converted configuration key</span></span> | <span data-ttu-id="03889-458">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="03889-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="03889-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-460">'Blazor'</span></span>
- <span data-ttu-id="03889-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-461">'Identity'</span></span>
- <span data-ttu-id="03889-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-463">'Razor'</span></span>
- <span data-ttu-id="03889-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-466">'Blazor'</span></span>
- <span data-ttu-id="03889-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-467">'Identity'</span></span>
- <span data-ttu-id="03889-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-469">'Razor'</span></span>
- <span data-ttu-id="03889-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-472">'Blazor'</span></span>
- <span data-ttu-id="03889-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-473">'Identity'</span></span>
- <span data-ttu-id="03889-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-475">'Razor'</span></span>
- <span data-ttu-id="03889-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-478">'Blazor'</span></span>
- <span data-ttu-id="03889-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-479">'Identity'</span></span>
- <span data-ttu-id="03889-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-481">'Razor'</span></span>
- <span data-ttu-id="03889-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-484">'Blazor'</span></span>
- <span data-ttu-id="03889-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-485">'Identity'</span></span>
- <span data-ttu-id="03889-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-487">'Razor'</span></span>
- <span data-ttu-id="03889-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-490">'Blazor'</span></span>
- <span data-ttu-id="03889-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-491">'Identity'</span></span>
- <span data-ttu-id="03889-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-493">'Razor'</span></span>
- <span data-ttu-id="03889-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-496">'Blazor'</span></span>
- <span data-ttu-id="03889-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-497">'Identity'</span></span>
- <span data-ttu-id="03889-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-499">'Razor'</span></span>
- <span data-ttu-id="03889-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-502">'Blazor'</span></span>
- <span data-ttu-id="03889-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-503">'Identity'</span></span>
- <span data-ttu-id="03889-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-505">'Razor'</span></span>
- <span data-ttu-id="03889-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-508">'Blazor'</span></span>
- <span data-ttu-id="03889-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-509">'Identity'</span></span>
- <span data-ttu-id="03889-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-511">'Razor'</span></span>
- <span data-ttu-id="03889-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-514">'Blazor'</span></span>
- <span data-ttu-id="03889-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-515">'Identity'</span></span>
- <span data-ttu-id="03889-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-517">'Razor'</span></span>
- <span data-ttu-id="03889-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-518">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-520">'Blazor'</span></span>
- <span data-ttu-id="03889-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-521">'Identity'</span></span>
- <span data-ttu-id="03889-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-523">'Razor'</span></span>
- <span data-ttu-id="03889-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-526">'Blazor'</span></span>
- <span data-ttu-id="03889-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-527">'Identity'</span></span>
- <span data-ttu-id="03889-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-529">'Razor'</span></span>
- <span data-ttu-id="03889-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-532">'Blazor'</span></span>
- <span data-ttu-id="03889-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-533">'Identity'</span></span>
- <span data-ttu-id="03889-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-535">'Razor'</span></span>
- <span data-ttu-id="03889-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-538">'Blazor'</span></span>
- <span data-ttu-id="03889-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-539">'Identity'</span></span>
- <span data-ttu-id="03889-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-541">'Razor'</span></span>
- <span data-ttu-id="03889-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-544">'Blazor'</span></span>
- <span data-ttu-id="03889-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-545">'Identity'</span></span>
- <span data-ttu-id="03889-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-547">'Razor'</span></span>
- <span data-ttu-id="03889-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-550">'Blazor'</span></span>
- <span data-ttu-id="03889-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-551">'Identity'</span></span>
- <span data-ttu-id="03889-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-553">'Razor'</span></span>
- <span data-ttu-id="03889-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-556">'Blazor'</span></span>
- <span data-ttu-id="03889-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-557">'Identity'</span></span>
- <span data-ttu-id="03889-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-559">'Razor'</span></span>
- <span data-ttu-id="03889-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-562">'Blazor'</span></span>
- <span data-ttu-id="03889-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-563">'Identity'</span></span>
- <span data-ttu-id="03889-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-565">'Razor'</span></span>
- <span data-ttu-id="03889-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-568">'Blazor'</span></span>
- <span data-ttu-id="03889-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-569">'Identity'</span></span>
- <span data-ttu-id="03889-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-571">'Razor'</span></span>
- <span data-ttu-id="03889-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-574">'Blazor'</span></span>
- <span data-ttu-id="03889-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-575">'Identity'</span></span>
- <span data-ttu-id="03889-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-577">'Razor'</span></span>
- <span data-ttu-id="03889-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-580">'Blazor'</span></span>
- <span data-ttu-id="03889-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-581">'Identity'</span></span>
- <span data-ttu-id="03889-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-583">'Razor'</span></span>
- <span data-ttu-id="03889-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-584">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-586">'Blazor'</span></span>
- <span data-ttu-id="03889-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-587">'Identity'</span></span>
- <span data-ttu-id="03889-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-589">'Razor'</span></span>
- <span data-ttu-id="03889-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-592">'Blazor'</span></span>
- <span data-ttu-id="03889-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-593">'Identity'</span></span>
- <span data-ttu-id="03889-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-595">'Razor'</span></span>
- <span data-ttu-id="03889-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-598">'Blazor'</span></span>
- <span data-ttu-id="03889-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-599">'Identity'</span></span>
- <span data-ttu-id="03889-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-601">'Razor'</span></span>
- <span data-ttu-id="03889-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-604">'Blazor'</span></span>
- <span data-ttu-id="03889-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-605">'Identity'</span></span>
- <span data-ttu-id="03889-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-607">'Razor'</span></span>
- <span data-ttu-id="03889-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-610">'Blazor'</span></span>
- <span data-ttu-id="03889-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-611">'Identity'</span></span>
- <span data-ttu-id="03889-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-613">'Razor'</span></span>
- <span data-ttu-id="03889-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-616">'Blazor'</span></span>
- <span data-ttu-id="03889-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-617">'Identity'</span></span>
- <span data-ttu-id="03889-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-619">'Razor'</span></span>
- <span data-ttu-id="03889-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-622">'Blazor'</span></span>
- <span data-ttu-id="03889-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-623">'Identity'</span></span>
- <span data-ttu-id="03889-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-625">'Razor'</span></span>
- <span data-ttu-id="03889-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-628">'Blazor'</span></span>
- <span data-ttu-id="03889-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-629">'Identity'</span></span>
- <span data-ttu-id="03889-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-631">'Razor'</span></span>
- <span data-ttu-id="03889-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-634">'Blazor'</span></span>
- <span data-ttu-id="03889-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-635">'Identity'</span></span>
- <span data-ttu-id="03889-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-637">'Razor'</span></span>
- <span data-ttu-id="03889-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-640">'Blazor'</span></span>
- <span data-ttu-id="03889-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-641">'Identity'</span></span>
- <span data-ttu-id="03889-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-643">'Razor'</span></span>
- <span data-ttu-id="03889-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-646">'Blazor'</span></span>
- <span data-ttu-id="03889-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-647">'Identity'</span></span>
- <span data-ttu-id="03889-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-649">'Razor'</span></span>
- <span data-ttu-id="03889-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-652">'Blazor'</span></span>
- <span data-ttu-id="03889-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-653">'Identity'</span></span>
- <span data-ttu-id="03889-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-655">'Razor'</span></span>
- <span data-ttu-id="03889-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-658">'Blazor'</span></span>
- <span data-ttu-id="03889-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-659">'Identity'</span></span>
- <span data-ttu-id="03889-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-661">'Razor'</span></span>
- <span data-ttu-id="03889-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-664">'Blazor'</span></span>
- <span data-ttu-id="03889-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-665">'Identity'</span></span>
- <span data-ttu-id="03889-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-667">'Razor'</span></span>
- <span data-ttu-id="03889-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-670">'Blazor'</span></span>
- <span data-ttu-id="03889-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-671">'Identity'</span></span>
- <span data-ttu-id="03889-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-673">'Razor'</span></span>
- <span data-ttu-id="03889-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-676">'Blazor'</span></span>
- <span data-ttu-id="03889-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-677">'Identity'</span></span>
- <span data-ttu-id="03889-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-679">'Razor'</span></span>
- <span data-ttu-id="03889-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-682">'Blazor'</span></span>
- <span data-ttu-id="03889-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-683">'Identity'</span></span>
- <span data-ttu-id="03889-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-685">'Razor'</span></span>
- <span data-ttu-id="03889-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-688">'Blazor'</span></span>
- <span data-ttu-id="03889-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-689">'Identity'</span></span>
- <span data-ttu-id="03889-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-691">'Razor'</span></span>
- <span data-ttu-id="03889-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-694">'Blazor'</span></span>
- <span data-ttu-id="03889-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-695">'Identity'</span></span>
- <span data-ttu-id="03889-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-697">'Razor'</span></span>
- <span data-ttu-id="03889-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-700">'Blazor'</span></span>
- <span data-ttu-id="03889-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-701">'Identity'</span></span>
- <span data-ttu-id="03889-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-703">'Razor'</span></span>
- <span data-ttu-id="03889-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-706">'Blazor'</span></span>
- <span data-ttu-id="03889-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-707">'Identity'</span></span>
- <span data-ttu-id="03889-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-709">'Razor'</span></span>
- <span data-ttu-id="03889-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-712">'Blazor'</span></span>
- <span data-ttu-id="03889-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-713">'Identity'</span></span>
- <span data-ttu-id="03889-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-715">'Razor'</span></span>
- <span data-ttu-id="03889-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-718">'Blazor'</span></span>
- <span data-ttu-id="03889-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-719">'Identity'</span></span>
- <span data-ttu-id="03889-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-721">'Razor'</span></span>
- <span data-ttu-id="03889-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-724">'Blazor'</span></span>
- <span data-ttu-id="03889-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-725">'Identity'</span></span>
- <span data-ttu-id="03889-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-727">'Razor'</span></span>
- <span data-ttu-id="03889-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-730">'Blazor'</span></span>
- <span data-ttu-id="03889-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-731">'Identity'</span></span>
- <span data-ttu-id="03889-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-733">'Razor'</span></span>
- <span data-ttu-id="03889-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-736">'Blazor'</span></span>
- <span data-ttu-id="03889-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-737">'Identity'</span></span>
- <span data-ttu-id="03889-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-739">'Razor'</span></span>
- <span data-ttu-id="03889-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-742">'Blazor'</span></span>
- <span data-ttu-id="03889-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-743">'Identity'</span></span>
- <span data-ttu-id="03889-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-745">'Razor'</span></span>
- <span data-ttu-id="03889-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-748">'Blazor'</span></span>
- <span data-ttu-id="03889-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-749">'Identity'</span></span>
- <span data-ttu-id="03889-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-751">'Razor'</span></span>
- <span data-ttu-id="03889-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-754">'Blazor'</span></span>
- <span data-ttu-id="03889-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-755">'Identity'</span></span>
- <span data-ttu-id="03889-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-757">'Razor'</span></span>
- <span data-ttu-id="03889-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-760">'Blazor'</span></span>
- <span data-ttu-id="03889-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-761">'Identity'</span></span>
- <span data-ttu-id="03889-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-763">'Razor'</span></span>
- <span data-ttu-id="03889-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-766">'Blazor'</span></span>
- <span data-ttu-id="03889-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-767">'Identity'</span></span>
- <span data-ttu-id="03889-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-769">'Razor'</span></span>
- <span data-ttu-id="03889-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-772">'Blazor'</span></span>
- <span data-ttu-id="03889-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-773">'Identity'</span></span>
- <span data-ttu-id="03889-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-775">'Razor'</span></span>
- <span data-ttu-id="03889-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-778">'Blazor'</span></span>
- <span data-ttu-id="03889-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-779">'Identity'</span></span>
- <span data-ttu-id="03889-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-781">'Razor'</span></span>
- <span data-ttu-id="03889-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-784">'Blazor'</span></span>
- <span data-ttu-id="03889-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-785">'Identity'</span></span>
- <span data-ttu-id="03889-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-787">'Razor'</span></span>
- <span data-ttu-id="03889-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-790">'Blazor'</span></span>
- <span data-ttu-id="03889-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-791">'Identity'</span></span>
- <span data-ttu-id="03889-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-793">'Razor'</span></span>
- <span data-ttu-id="03889-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-796">'Blazor'</span></span>
- <span data-ttu-id="03889-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-797">'Identity'</span></span>
- <span data-ttu-id="03889-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-799">'Razor'</span></span>
- <span data-ttu-id="03889-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-802">'Blazor'</span></span>
- <span data-ttu-id="03889-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-803">'Identity'</span></span>
- <span data-ttu-id="03889-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-805">'Razor'</span></span>
- <span data-ttu-id="03889-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-806">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="03889-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="03889-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-809">値: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-810">値: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-811">値: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="03889-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="03889-812">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-812">JSON configuration provider</span></span>

<span data-ttu-id="03889-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="03889-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="03889-814">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="03889-814">Overloads can specify:</span></span>

* <span data-ttu-id="03889-815">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-815">Whether the file is optional.</span></span>
* <span data-ttu-id="03889-816">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="03889-817">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="03889-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="03889-818">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="03889-818">The preceding code:</span></span>

* <span data-ttu-id="03889-819">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="03889-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="03889-820">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="03889-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="03889-821">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="03889-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="03889-822">*MyConfig.json* ファイルの前に[既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="03889-823">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="03889-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03889-824">通常は、[環境変数構成プロバイダー](#evcp)および[コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="03889-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03889-825">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="03889-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="03889-826">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="03889-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="03889-827">*appsettings.json* と *appsettings*.`Environment`.*json* ファイルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="03889-828">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03889-829">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="03889-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="03889-830">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="03889-831">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-831">File configuration provider</span></span>

<span data-ttu-id="03889-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="03889-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="03889-833">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="03889-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="03889-834">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="03889-835">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="03889-836">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="03889-837">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-837">INI configuration provider</span></span>

<span data-ttu-id="03889-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-839">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="03889-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="03889-840">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="03889-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="03889-841">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="03889-842">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="03889-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03889-843">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="03889-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="03889-844">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="03889-845">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-845">XML configuration provider</span></span>

<span data-ttu-id="03889-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-847">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="03889-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="03889-848">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="03889-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="03889-849">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="03889-850">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="03889-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="03889-851">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="03889-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="03889-852">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-853">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="03889-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="03889-854">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="03889-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-855">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="03889-856">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03889-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="03889-857">key:attribute</span></span>
* <span data-ttu-id="03889-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="03889-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="03889-859">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="03889-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="03889-861">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="03889-861">The key is the file name.</span></span> <span data-ttu-id="03889-862">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-862">The value contains the file's contents.</span></span> <span data-ttu-id="03889-863">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="03889-864">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="03889-865">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="03889-866">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="03889-867">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="03889-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="03889-868">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="03889-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="03889-869">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="03889-870">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="03889-871">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="03889-872">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-872">Memory configuration provider</span></span>

<span data-ttu-id="03889-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="03889-874">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="03889-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="03889-875">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="03889-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-876">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="03889-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="03889-877">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="03889-878">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="03889-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="03889-879">GetValue</span></span>

<span data-ttu-id="03889-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="03889-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-881">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="03889-882">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="03889-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="03889-883">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="03889-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="03889-884">以下のコードでは、*MySubsection セクション*を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="03889-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="03889-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="03889-885">GetSection</span></span>

<span data-ttu-id="03889-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="03889-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="03889-887">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-888">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-889">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="03889-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="03889-890">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="03889-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="03889-891">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="03889-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="03889-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="03889-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="03889-893">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="03889-893">GetChildren and Exists</span></span>

<span data-ttu-id="03889-894">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-895">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="03889-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="03889-896">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="03889-896">Bind an array</span></span>

<span data-ttu-id="03889-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="03889-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03889-898">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="03889-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="03889-899">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="03889-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="03889-900">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="03889-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="03889-901">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="03889-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-902">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="03889-903">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="03889-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="03889-904">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="03889-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="03889-905">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="03889-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="03889-906">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="03889-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="03889-907">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="03889-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-908">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="03889-909">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="03889-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="03889-910">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="03889-911">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="03889-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="03889-912">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="03889-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="03889-913">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="03889-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="03889-914">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="03889-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="03889-915">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="03889-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-916">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="03889-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="03889-917">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="03889-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="03889-918">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-918">Custom configuration provider</span></span>

<span data-ttu-id="03889-919">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="03889-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="03889-920">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="03889-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="03889-921">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="03889-922">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="03889-923">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="03889-924">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="03889-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="03889-925">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="03889-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="03889-926">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="03889-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="03889-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="03889-928">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="03889-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="03889-930"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="03889-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="03889-932"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="03889-933">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="03889-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="03889-934">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="03889-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="03889-936">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="03889-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="03889-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="03889-938">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="03889-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="03889-939">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="03889-939">Access configuration in Startup</span></span>

<span data-ttu-id="03889-940">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="03889-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="03889-941">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="03889-942">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="03889-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="03889-943">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="03889-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="03889-944">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="03889-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="03889-945">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="03889-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="03889-946">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="03889-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="03889-947">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="03889-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="03889-948">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="03889-948">Configure options with a delegate</span></span>

<span data-ttu-id="03889-949">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="03889-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="03889-950">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="03889-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="03889-951">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="03889-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="03889-952">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="03889-953">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="03889-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="03889-954">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="03889-955">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="03889-955">Host versus app configuration</span></span>

<span data-ttu-id="03889-956">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="03889-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="03889-957">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="03889-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="03889-958">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="03889-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="03889-959">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="03889-960">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="03889-961">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="03889-961">Default host configuration</span></span>

<span data-ttu-id="03889-962">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="03889-963">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="03889-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="03889-964">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="03889-965">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="03889-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="03889-966">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="03889-967">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="03889-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="03889-968">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="03889-969">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="03889-970">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="03889-971">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="03889-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="03889-972">その他の構成</span><span class="sxs-lookup"><span data-stu-id="03889-972">Other configuration</span></span>

<span data-ttu-id="03889-973">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="03889-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="03889-974">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="03889-975">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="03889-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="03889-976"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="03889-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="03889-977">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="03889-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="03889-978">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="03889-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="03889-979">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="03889-980">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="03889-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="03889-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="03889-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="03889-982">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03889-983">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="03889-983">Additional resources</span></span>

* [<span data-ttu-id="03889-984">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="03889-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03889-985">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="03889-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="03889-986">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="03889-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="03889-987">Azure Key Vault</span></span>
* <span data-ttu-id="03889-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="03889-988">Azure App Configuration</span></span>
* <span data-ttu-id="03889-989">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="03889-989">Command-line arguments</span></span>
* <span data-ttu-id="03889-990">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="03889-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="03889-991">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="03889-991">Directory files</span></span>
* <span data-ttu-id="03889-992">環境変数</span><span class="sxs-lookup"><span data-stu-id="03889-992">Environment variables</span></span>
* <span data-ttu-id="03889-993">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="03889-993">In-memory .NET objects</span></span>
* <span data-ttu-id="03889-994">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="03889-994">Settings files</span></span>

<span data-ttu-id="03889-995">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="03889-996">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="03889-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="03889-997">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="03889-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="03889-998">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="03889-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="03889-999">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="03889-1000">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="03889-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="03889-1001">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="03889-1001">Host versus app configuration</span></span>

<span data-ttu-id="03889-1002">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="03889-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="03889-1003">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="03889-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="03889-1004">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="03889-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="03889-1005">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="03889-1006">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="03889-1007">その他の構成</span><span class="sxs-lookup"><span data-stu-id="03889-1007">Other configuration</span></span>

<span data-ttu-id="03889-1008">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="03889-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="03889-1009">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="03889-1010">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="03889-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="03889-1011"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="03889-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="03889-1012">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="03889-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="03889-1013">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="03889-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="03889-1014">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="03889-1015">既定の構成</span><span class="sxs-lookup"><span data-stu-id="03889-1015">Default configuration</span></span>

<span data-ttu-id="03889-1016">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="03889-1017">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="03889-1018">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="03889-1019">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="03889-1020">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="03889-1021">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="03889-1022">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="03889-1023">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="03889-1024">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="03889-1025">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="03889-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="03889-1026">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="03889-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="03889-1027">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="03889-1028">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="03889-1029">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="03889-1030">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="03889-1030">Security</span></span>

<span data-ttu-id="03889-1031">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="03889-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="03889-1032">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="03889-1033">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="03889-1034">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="03889-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="03889-1035">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="03889-1036"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="03889-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="03889-1037">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="03889-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="03889-1038">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="03889-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="03889-1040">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="03889-1041">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="03889-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="03889-1042">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="03889-1043">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="03889-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="03889-1044">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="03889-1045">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="03889-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03889-1046">section0:key0</span></span>
* <span data-ttu-id="03889-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03889-1047">section0:key1</span></span>
* <span data-ttu-id="03889-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03889-1048">section1:key0</span></span>
* <span data-ttu-id="03889-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03889-1049">section1:key1</span></span>

<span data-ttu-id="03889-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="03889-1051">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="03889-1052">規約</span><span class="sxs-lookup"><span data-stu-id="03889-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="03889-1053">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1053">Sources and providers</span></span>

<span data-ttu-id="03889-1054">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="03889-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="03889-1055">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="03889-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="03889-1056">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="03889-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="03889-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="03889-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="03889-1059">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="03889-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="03889-1060">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="03889-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="03889-1061">キー</span><span class="sxs-lookup"><span data-stu-id="03889-1061">Keys</span></span>

<span data-ttu-id="03889-1062">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="03889-1063">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="03889-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="03889-1064">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="03889-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="03889-1065">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="03889-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="03889-1066">階層キー</span><span class="sxs-lookup"><span data-stu-id="03889-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="03889-1067">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="03889-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="03889-1068">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="03889-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="03889-1069">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="03889-1070">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="03889-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="03889-1071">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="03889-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="03889-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="03889-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03889-1073">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="03889-1074">値</span><span class="sxs-lookup"><span data-stu-id="03889-1074">Values</span></span>

<span data-ttu-id="03889-1075">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="03889-1076">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="03889-1076">Values are strings.</span></span>
* <span data-ttu-id="03889-1077">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="03889-1078">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1078">Providers</span></span>

<span data-ttu-id="03889-1079">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="03889-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="03889-1080">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1080">Provider</span></span> | <span data-ttu-id="03889-1081">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="03889-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="03889-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1083">'Blazor'</span></span>
- <span data-ttu-id="03889-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1084">'Identity'</span></span>
- <span data-ttu-id="03889-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1086">'Razor'</span></span>
- <span data-ttu-id="03889-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1089">'Blazor'</span></span>
- <span data-ttu-id="03889-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1090">'Identity'</span></span>
- <span data-ttu-id="03889-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1092">'Razor'</span></span>
- <span data-ttu-id="03889-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1095">'Blazor'</span></span>
- <span data-ttu-id="03889-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1096">'Identity'</span></span>
- <span data-ttu-id="03889-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1098">'Razor'</span></span>
- <span data-ttu-id="03889-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1101">'Blazor'</span></span>
- <span data-ttu-id="03889-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1102">'Identity'</span></span>
- <span data-ttu-id="03889-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1104">'Razor'</span></span>
- <span data-ttu-id="03889-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1107">'Blazor'</span></span>
- <span data-ttu-id="03889-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1108">'Identity'</span></span>
- <span data-ttu-id="03889-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1110">'Razor'</span></span>
- <span data-ttu-id="03889-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1113">'Blazor'</span></span>
- <span data-ttu-id="03889-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1114">'Identity'</span></span>
- <span data-ttu-id="03889-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1116">'Razor'</span></span>
- <span data-ttu-id="03889-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1119">'Blazor'</span></span>
- <span data-ttu-id="03889-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1120">'Identity'</span></span>
- <span data-ttu-id="03889-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1122">'Razor'</span></span>
- <span data-ttu-id="03889-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1125">'Blazor'</span></span>
- <span data-ttu-id="03889-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1126">'Identity'</span></span>
- <span data-ttu-id="03889-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1128">'Razor'</span></span>
- <span data-ttu-id="03889-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1131">'Blazor'</span></span>
- <span data-ttu-id="03889-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1132">'Identity'</span></span>
- <span data-ttu-id="03889-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1134">'Razor'</span></span>
- <span data-ttu-id="03889-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1137">'Blazor'</span></span>
- <span data-ttu-id="03889-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1138">'Identity'</span></span>
- <span data-ttu-id="03889-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1140">'Razor'</span></span>
- <span data-ttu-id="03889-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1143">'Blazor'</span></span>
- <span data-ttu-id="03889-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1144">'Identity'</span></span>
- <span data-ttu-id="03889-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1146">'Razor'</span></span>
- <span data-ttu-id="03889-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1149">'Blazor'</span></span>
- <span data-ttu-id="03889-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1150">'Identity'</span></span>
- <span data-ttu-id="03889-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1152">'Razor'</span></span>
- <span data-ttu-id="03889-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1155">'Blazor'</span></span>
- <span data-ttu-id="03889-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1156">'Identity'</span></span>
- <span data-ttu-id="03889-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1158">'Razor'</span></span>
- <span data-ttu-id="03889-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1161">'Blazor'</span></span>
- <span data-ttu-id="03889-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1162">'Identity'</span></span>
- <span data-ttu-id="03889-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1164">'Razor'</span></span>
- <span data-ttu-id="03889-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1167">'Blazor'</span></span>
- <span data-ttu-id="03889-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1168">'Identity'</span></span>
- <span data-ttu-id="03889-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1170">'Razor'</span></span>
- <span data-ttu-id="03889-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1173">'Blazor'</span></span>
- <span data-ttu-id="03889-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1174">'Identity'</span></span>
- <span data-ttu-id="03889-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1176">'Razor'</span></span>
- <span data-ttu-id="03889-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1179">'Blazor'</span></span>
- <span data-ttu-id="03889-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1180">'Identity'</span></span>
- <span data-ttu-id="03889-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1182">'Razor'</span></span>
- <span data-ttu-id="03889-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1184">------------------ | | [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) (*セキュリティ*のトピック) | Azure Key Vault | | [Azure アプリ構成プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure ドキュメント) | Azure App Configuration | | [コマンドライン構成プロバイダー](#command-line-configuration-provider) | コマンドライン パラメーター | | [カスタム構成プロバイダー](#custom-configuration-provider) | カスタム ソース | | [環境変数構成プロバイダー](#environment-variables-configuration-provider) | 環境変数 | | [ファイル構成プロバイダー](#file-configuration-provider) | ファイル (INI、JSON、XML) | | [ファイルごとのキーの構成プロバイダー](#key-per-file-configuration-provider) | ディレクトリ ファイル | | [メモリ構成プロバイダー](#memory-configuration-provider) | メモリ内コレクション | | [ユーザー シークレット (シークレット マネージャー)](xref:security/app-secrets) (*セキュリティ*のトピック) | ユーザー プロファイル ディレクトリのファイル |</span><span class="sxs-lookup"><span data-stu-id="03889-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="03889-1185">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="03889-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="03889-1186">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="03889-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="03889-1187">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="03889-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="03889-1188">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="03889-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="03889-1189">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="03889-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="03889-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="03889-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="03889-1191">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="03889-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="03889-1192">環境変数</span><span class="sxs-lookup"><span data-stu-id="03889-1192">Environment variables</span></span>
1. <span data-ttu-id="03889-1193">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="03889-1193">Command-line arguments</span></span>

<span data-ttu-id="03889-1194">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="03889-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="03889-1195">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03889-1196">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="03889-1197">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="03889-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="03889-1198">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="03889-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="03889-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="03889-1200">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="03889-1201">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="03889-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="03889-1202">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="03889-1203">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="03889-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="03889-1204">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="03889-1205">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="03889-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="03889-1206">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03889-1207">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="03889-1208">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="03889-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-1210">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03889-1211">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="03889-1212">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03889-1213">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03889-1214">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="03889-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="03889-1215">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03889-1216">環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-1216">Environment variables.</span></span>

<span data-ttu-id="03889-1217">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="03889-1218">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="03889-1219">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="03889-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="03889-1220">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="03889-1221">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="03889-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03889-1222">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="03889-1223">**例**</span><span class="sxs-lookup"><span data-stu-id="03889-1223">**Example**</span></span>

<span data-ttu-id="03889-1224">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="03889-1225">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="03889-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="03889-1226">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="03889-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="03889-1227">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="03889-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03889-1228">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="03889-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="03889-1229">引数</span><span class="sxs-lookup"><span data-stu-id="03889-1229">Arguments</span></span>

<span data-ttu-id="03889-1230">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="03889-1231">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="03889-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="03889-1232">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="03889-1232">Key prefix</span></span>               | <span data-ttu-id="03889-1233">例</span><span class="sxs-lookup"><span data-stu-id="03889-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="03889-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1235">'Blazor'</span></span>
- <span data-ttu-id="03889-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1236">'Identity'</span></span>
- <span data-ttu-id="03889-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1238">'Razor'</span></span>
- <span data-ttu-id="03889-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1241">'Blazor'</span></span>
- <span data-ttu-id="03889-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1242">'Identity'</span></span>
- <span data-ttu-id="03889-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1244">'Razor'</span></span>
- <span data-ttu-id="03889-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1247">'Blazor'</span></span>
- <span data-ttu-id="03889-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1248">'Identity'</span></span>
- <span data-ttu-id="03889-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1250">'Razor'</span></span>
- <span data-ttu-id="03889-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1253">'Blazor'</span></span>
- <span data-ttu-id="03889-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1254">'Identity'</span></span>
- <span data-ttu-id="03889-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1256">'Razor'</span></span>
- <span data-ttu-id="03889-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1259">'Blazor'</span></span>
- <span data-ttu-id="03889-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1260">'Identity'</span></span>
- <span data-ttu-id="03889-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1262">'Razor'</span></span>
- <span data-ttu-id="03889-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1265">'Blazor'</span></span>
- <span data-ttu-id="03889-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1266">'Identity'</span></span>
- <span data-ttu-id="03889-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1268">'Razor'</span></span>
- <span data-ttu-id="03889-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1271">'Blazor'</span></span>
- <span data-ttu-id="03889-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1272">'Identity'</span></span>
- <span data-ttu-id="03889-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1274">'Razor'</span></span>
- <span data-ttu-id="03889-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1277">'Blazor'</span></span>
- <span data-ttu-id="03889-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1278">'Identity'</span></span>
- <span data-ttu-id="03889-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1280">'Razor'</span></span>
- <span data-ttu-id="03889-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1283">'Blazor'</span></span>
- <span data-ttu-id="03889-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1284">'Identity'</span></span>
- <span data-ttu-id="03889-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1286">'Razor'</span></span>
- <span data-ttu-id="03889-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1289">'Blazor'</span></span>
- <span data-ttu-id="03889-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1290">'Identity'</span></span>
- <span data-ttu-id="03889-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1292">'Razor'</span></span>
- <span data-ttu-id="03889-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1295">'Blazor'</span></span>
- <span data-ttu-id="03889-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1296">'Identity'</span></span>
- <span data-ttu-id="03889-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1298">'Razor'</span></span>
- <span data-ttu-id="03889-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1301">'Blazor'</span></span>
- <span data-ttu-id="03889-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1302">'Identity'</span></span>
- <span data-ttu-id="03889-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1304">'Razor'</span></span>
- <span data-ttu-id="03889-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1307">'Blazor'</span></span>
- <span data-ttu-id="03889-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1308">'Identity'</span></span>
- <span data-ttu-id="03889-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1310">'Razor'</span></span>
- <span data-ttu-id="03889-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1313">'Blazor'</span></span>
- <span data-ttu-id="03889-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1314">'Identity'</span></span>
- <span data-ttu-id="03889-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1316">'Razor'</span></span>
- <span data-ttu-id="03889-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1319">'Blazor'</span></span>
- <span data-ttu-id="03889-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1320">'Identity'</span></span>
- <span data-ttu-id="03889-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1322">'Razor'</span></span>
- <span data-ttu-id="03889-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1325">'Blazor'</span></span>
- <span data-ttu-id="03889-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1326">'Identity'</span></span>
- <span data-ttu-id="03889-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1328">'Razor'</span></span>
- <span data-ttu-id="03889-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1331">'Blazor'</span></span>
- <span data-ttu-id="03889-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1332">'Identity'</span></span>
- <span data-ttu-id="03889-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1334">'Razor'</span></span>
- <span data-ttu-id="03889-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1337">'Blazor'</span></span>
- <span data-ttu-id="03889-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1338">'Identity'</span></span>
- <span data-ttu-id="03889-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1340">'Razor'</span></span>
- <span data-ttu-id="03889-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1343">'Blazor'</span></span>
- <span data-ttu-id="03889-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1344">'Identity'</span></span>
- <span data-ttu-id="03889-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1346">'Razor'</span></span>
- <span data-ttu-id="03889-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1349">'Blazor'</span></span>
- <span data-ttu-id="03889-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1350">'Identity'</span></span>
- <span data-ttu-id="03889-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1352">'Razor'</span></span>
- <span data-ttu-id="03889-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1355">'Blazor'</span></span>
- <span data-ttu-id="03889-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1356">'Identity'</span></span>
- <span data-ttu-id="03889-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1358">'Razor'</span></span>
- <span data-ttu-id="03889-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1361">'Blazor'</span></span>
- <span data-ttu-id="03889-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1362">'Identity'</span></span>
- <span data-ttu-id="03889-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1364">'Razor'</span></span>
- <span data-ttu-id="03889-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1367">'Blazor'</span></span>
- <span data-ttu-id="03889-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1368">'Identity'</span></span>
- <span data-ttu-id="03889-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1370">'Razor'</span></span>
- <span data-ttu-id="03889-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1373">'Blazor'</span></span>
- <span data-ttu-id="03889-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1374">'Identity'</span></span>
- <span data-ttu-id="03889-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1376">'Razor'</span></span>
- <span data-ttu-id="03889-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1379">'Blazor'</span></span>
- <span data-ttu-id="03889-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1380">'Identity'</span></span>
- <span data-ttu-id="03889-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1382">'Razor'</span></span>
- <span data-ttu-id="03889-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1385">'Blazor'</span></span>
- <span data-ttu-id="03889-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1386">'Identity'</span></span>
- <span data-ttu-id="03889-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1388">'Razor'</span></span>
- <span data-ttu-id="03889-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1391">'Blazor'</span></span>
- <span data-ttu-id="03889-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1392">'Identity'</span></span>
- <span data-ttu-id="03889-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1394">'Razor'</span></span>
- <span data-ttu-id="03889-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1397">'Blazor'</span></span>
- <span data-ttu-id="03889-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1398">'Identity'</span></span>
- <span data-ttu-id="03889-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1400">'Razor'</span></span>
- <span data-ttu-id="03889-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1403">'Blazor'</span></span>
- <span data-ttu-id="03889-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1404">'Identity'</span></span>
- <span data-ttu-id="03889-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1406">'Razor'</span></span>
- <span data-ttu-id="03889-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1409">'Blazor'</span></span>
- <span data-ttu-id="03889-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1410">'Identity'</span></span>
- <span data-ttu-id="03889-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1412">'Razor'</span></span>
- <span data-ttu-id="03889-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1415">'Blazor'</span></span>
- <span data-ttu-id="03889-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1416">'Identity'</span></span>
- <span data-ttu-id="03889-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1418">'Razor'</span></span>
- <span data-ttu-id="03889-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1421">'Blazor'</span></span>
- <span data-ttu-id="03889-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1422">'Identity'</span></span>
- <span data-ttu-id="03889-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1424">'Razor'</span></span>
- <span data-ttu-id="03889-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1427">'Blazor'</span></span>
- <span data-ttu-id="03889-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1428">'Identity'</span></span>
- <span data-ttu-id="03889-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1430">'Razor'</span></span>
- <span data-ttu-id="03889-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1433">'Blazor'</span></span>
- <span data-ttu-id="03889-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1434">'Identity'</span></span>
- <span data-ttu-id="03889-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1436">'Razor'</span></span>
- <span data-ttu-id="03889-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1439">'Blazor'</span></span>
- <span data-ttu-id="03889-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1440">'Identity'</span></span>
- <span data-ttu-id="03889-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1442">'Razor'</span></span>
- <span data-ttu-id="03889-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1444">--------------------------- | | プレフィックスなし                | `CommandLineKey1=value1`                               |
| 2 つのダッシュ (`--`)        | `--CommandLineKey2=value2`、`--CommandLineKey2 value2` |
| フォワード スラッシュ (`/`)      | `/CommandLineKey3=value3`、`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="03889-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="03889-1445">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="03889-1446">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="03889-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="03889-1447">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="03889-1447">Switch mappings</span></span>

<span data-ttu-id="03889-1448">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="03889-1449"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="03889-1450">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="03889-1451">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="03889-1452">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="03889-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="03889-1453">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="03889-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="03889-1454">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="03889-1455">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="03889-1456">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="03889-1457">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="03889-1458">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="03889-1459">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="03889-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="03889-1460">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="03889-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03889-1461">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="03889-1462">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="03889-1463">Key</span><span class="sxs-lookup"><span data-stu-id="03889-1463">Key</span></span>       | <span data-ttu-id="03889-1464">[値]</span><span class="sxs-lookup"><span data-stu-id="03889-1464">Value</span></span>             |
| ---
<span data-ttu-id="03889-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1466">'Blazor'</span></span>
- <span data-ttu-id="03889-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1467">'Identity'</span></span>
- <span data-ttu-id="03889-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1469">'Razor'</span></span>
- <span data-ttu-id="03889-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1472">'Blazor'</span></span>
- <span data-ttu-id="03889-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1473">'Identity'</span></span>
- <span data-ttu-id="03889-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1475">'Razor'</span></span>
- <span data-ttu-id="03889-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1478">'Blazor'</span></span>
- <span data-ttu-id="03889-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1479">'Identity'</span></span>
- <span data-ttu-id="03889-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1481">'Razor'</span></span>
- <span data-ttu-id="03889-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1484">'Blazor'</span></span>
- <span data-ttu-id="03889-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1485">'Identity'</span></span>
- <span data-ttu-id="03889-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1487">'Razor'</span></span>
- <span data-ttu-id="03889-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1490">'Blazor'</span></span>
- <span data-ttu-id="03889-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1491">'Identity'</span></span>
- <span data-ttu-id="03889-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1493">'Razor'</span></span>
- <span data-ttu-id="03889-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1496">'Blazor'</span></span>
- <span data-ttu-id="03889-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1497">'Identity'</span></span>
- <span data-ttu-id="03889-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1499">'Razor'</span></span>
- <span data-ttu-id="03889-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1502">'Blazor'</span></span>
- <span data-ttu-id="03889-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1503">'Identity'</span></span>
- <span data-ttu-id="03889-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1505">'Razor'</span></span>
- <span data-ttu-id="03889-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1508">'Blazor'</span></span>
- <span data-ttu-id="03889-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1509">'Identity'</span></span>
- <span data-ttu-id="03889-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1511">'Razor'</span></span>
- <span data-ttu-id="03889-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="03889-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="03889-1514">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="03889-1515">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="03889-1516">Key</span><span class="sxs-lookup"><span data-stu-id="03889-1516">Key</span></span>               | <span data-ttu-id="03889-1517">[値]</span><span class="sxs-lookup"><span data-stu-id="03889-1517">Value</span></span>    |
| ---
<span data-ttu-id="03889-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1519">'Blazor'</span></span>
- <span data-ttu-id="03889-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1520">'Identity'</span></span>
- <span data-ttu-id="03889-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1522">'Razor'</span></span>
- <span data-ttu-id="03889-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1525">'Blazor'</span></span>
- <span data-ttu-id="03889-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1526">'Identity'</span></span>
- <span data-ttu-id="03889-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1528">'Razor'</span></span>
- <span data-ttu-id="03889-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1531">'Blazor'</span></span>
- <span data-ttu-id="03889-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1532">'Identity'</span></span>
- <span data-ttu-id="03889-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1534">'Razor'</span></span>
- <span data-ttu-id="03889-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1537">'Blazor'</span></span>
- <span data-ttu-id="03889-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1538">'Identity'</span></span>
- <span data-ttu-id="03889-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1540">'Razor'</span></span>
- <span data-ttu-id="03889-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1543">'Blazor'</span></span>
- <span data-ttu-id="03889-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1544">'Identity'</span></span>
- <span data-ttu-id="03889-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1546">'Razor'</span></span>
- <span data-ttu-id="03889-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1549">'Blazor'</span></span>
- <span data-ttu-id="03889-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1550">'Identity'</span></span>
- <span data-ttu-id="03889-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1552">'Razor'</span></span>
- <span data-ttu-id="03889-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1555">'Blazor'</span></span>
- <span data-ttu-id="03889-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1556">'Identity'</span></span>
- <span data-ttu-id="03889-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1558">'Razor'</span></span>
- <span data-ttu-id="03889-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1561">'Blazor'</span></span>
- <span data-ttu-id="03889-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1562">'Identity'</span></span>
- <span data-ttu-id="03889-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1564">'Razor'</span></span>
- <span data-ttu-id="03889-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="03889-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="03889-1567">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="03889-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-1569">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="03889-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="03889-1571">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="03889-1572">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="03889-1573">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03889-1574">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03889-1575">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="03889-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="03889-1576">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="03889-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="03889-1577">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03889-1578">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-1578">Command-line arguments.</span></span>

<span data-ttu-id="03889-1579">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="03889-1580">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="03889-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="03889-1581">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="03889-1582">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="03889-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="03889-1583">**例**</span><span class="sxs-lookup"><span data-stu-id="03889-1583">**Example**</span></span>

<span data-ttu-id="03889-1584">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="03889-1585">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="03889-1585">Run the sample app.</span></span> <span data-ttu-id="03889-1586">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="03889-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03889-1587">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="03889-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="03889-1588">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="03889-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="03889-1589">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="03889-1590">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="03889-1591">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="03889-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="03889-1592">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="03889-1592">Prefixes</span></span>

<span data-ttu-id="03889-1593">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="03889-1594">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="03889-1595">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="03889-1596">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="03889-1597">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03889-1598">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="03889-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="03889-1599">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="03889-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="03889-1600">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="03889-1601">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="03889-1601">Connection string prefix</span></span> | <span data-ttu-id="03889-1602">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-1602">Provider</span></span> |
| ---
<span data-ttu-id="03889-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1604">'Blazor'</span></span>
- <span data-ttu-id="03889-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1605">'Identity'</span></span>
- <span data-ttu-id="03889-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1607">'Razor'</span></span>
- <span data-ttu-id="03889-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1610">'Blazor'</span></span>
- <span data-ttu-id="03889-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1611">'Identity'</span></span>
- <span data-ttu-id="03889-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1613">'Razor'</span></span>
- <span data-ttu-id="03889-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1616">'Blazor'</span></span>
- <span data-ttu-id="03889-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1617">'Identity'</span></span>
- <span data-ttu-id="03889-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1619">'Razor'</span></span>
- <span data-ttu-id="03889-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1622">'Blazor'</span></span>
- <span data-ttu-id="03889-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1623">'Identity'</span></span>
- <span data-ttu-id="03889-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1625">'Razor'</span></span>
- <span data-ttu-id="03889-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1628">'Blazor'</span></span>
- <span data-ttu-id="03889-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1629">'Identity'</span></span>
- <span data-ttu-id="03889-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1631">'Razor'</span></span>
- <span data-ttu-id="03889-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1634">'Blazor'</span></span>
- <span data-ttu-id="03889-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1635">'Identity'</span></span>
- <span data-ttu-id="03889-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1637">'Razor'</span></span>
- <span data-ttu-id="03889-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1640">'Blazor'</span></span>
- <span data-ttu-id="03889-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1641">'Identity'</span></span>
- <span data-ttu-id="03889-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1643">'Razor'</span></span>
- <span data-ttu-id="03889-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1646">'Blazor'</span></span>
- <span data-ttu-id="03889-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1647">'Identity'</span></span>
- <span data-ttu-id="03889-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1649">'Razor'</span></span>
- <span data-ttu-id="03889-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1652">'Blazor'</span></span>
- <span data-ttu-id="03889-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1653">'Identity'</span></span>
- <span data-ttu-id="03889-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1655">'Razor'</span></span>
- <span data-ttu-id="03889-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1658">'Blazor'</span></span>
- <span data-ttu-id="03889-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1659">'Identity'</span></span>
- <span data-ttu-id="03889-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1661">'Razor'</span></span>
- <span data-ttu-id="03889-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1664">'Blazor'</span></span>
- <span data-ttu-id="03889-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1665">'Identity'</span></span>
- <span data-ttu-id="03889-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1667">'Razor'</span></span>
- <span data-ttu-id="03889-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1670">'Blazor'</span></span>
- <span data-ttu-id="03889-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1671">'Identity'</span></span>
- <span data-ttu-id="03889-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1673">'Razor'</span></span>
- <span data-ttu-id="03889-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1675">---- | | `CUSTOMCONNSTR_` | カスタム プロバイダー | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="03889-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="03889-1676">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="03889-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="03889-1677">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="03889-1678">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="03889-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="03889-1679">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="03889-1679">Environment variable key</span></span> | <span data-ttu-id="03889-1680">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="03889-1680">Converted configuration key</span></span> | <span data-ttu-id="03889-1681">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="03889-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="03889-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1683">'Blazor'</span></span>
- <span data-ttu-id="03889-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1684">'Identity'</span></span>
- <span data-ttu-id="03889-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1686">'Razor'</span></span>
- <span data-ttu-id="03889-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1689">'Blazor'</span></span>
- <span data-ttu-id="03889-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1690">'Identity'</span></span>
- <span data-ttu-id="03889-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1692">'Razor'</span></span>
- <span data-ttu-id="03889-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1695">'Blazor'</span></span>
- <span data-ttu-id="03889-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1696">'Identity'</span></span>
- <span data-ttu-id="03889-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1698">'Razor'</span></span>
- <span data-ttu-id="03889-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1701">'Blazor'</span></span>
- <span data-ttu-id="03889-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1702">'Identity'</span></span>
- <span data-ttu-id="03889-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1704">'Razor'</span></span>
- <span data-ttu-id="03889-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1707">'Blazor'</span></span>
- <span data-ttu-id="03889-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1708">'Identity'</span></span>
- <span data-ttu-id="03889-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1710">'Razor'</span></span>
- <span data-ttu-id="03889-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1713">'Blazor'</span></span>
- <span data-ttu-id="03889-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1714">'Identity'</span></span>
- <span data-ttu-id="03889-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1716">'Razor'</span></span>
- <span data-ttu-id="03889-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1719">'Blazor'</span></span>
- <span data-ttu-id="03889-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1720">'Identity'</span></span>
- <span data-ttu-id="03889-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1722">'Razor'</span></span>
- <span data-ttu-id="03889-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1725">'Blazor'</span></span>
- <span data-ttu-id="03889-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1726">'Identity'</span></span>
- <span data-ttu-id="03889-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1728">'Razor'</span></span>
- <span data-ttu-id="03889-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1731">'Blazor'</span></span>
- <span data-ttu-id="03889-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1732">'Identity'</span></span>
- <span data-ttu-id="03889-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1734">'Razor'</span></span>
- <span data-ttu-id="03889-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1737">'Blazor'</span></span>
- <span data-ttu-id="03889-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1738">'Identity'</span></span>
- <span data-ttu-id="03889-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1740">'Razor'</span></span>
- <span data-ttu-id="03889-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1743">'Blazor'</span></span>
- <span data-ttu-id="03889-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1744">'Identity'</span></span>
- <span data-ttu-id="03889-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1746">'Razor'</span></span>
- <span data-ttu-id="03889-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1749">'Blazor'</span></span>
- <span data-ttu-id="03889-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1750">'Identity'</span></span>
- <span data-ttu-id="03889-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1752">'Razor'</span></span>
- <span data-ttu-id="03889-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1755">'Blazor'</span></span>
- <span data-ttu-id="03889-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1756">'Identity'</span></span>
- <span data-ttu-id="03889-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1758">'Razor'</span></span>
- <span data-ttu-id="03889-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1761">'Blazor'</span></span>
- <span data-ttu-id="03889-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1762">'Identity'</span></span>
- <span data-ttu-id="03889-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1764">'Razor'</span></span>
- <span data-ttu-id="03889-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1767">'Blazor'</span></span>
- <span data-ttu-id="03889-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1768">'Identity'</span></span>
- <span data-ttu-id="03889-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1770">'Razor'</span></span>
- <span data-ttu-id="03889-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1773">'Blazor'</span></span>
- <span data-ttu-id="03889-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1774">'Identity'</span></span>
- <span data-ttu-id="03889-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1776">'Razor'</span></span>
- <span data-ttu-id="03889-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1779">'Blazor'</span></span>
- <span data-ttu-id="03889-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1780">'Identity'</span></span>
- <span data-ttu-id="03889-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1782">'Razor'</span></span>
- <span data-ttu-id="03889-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1785">'Blazor'</span></span>
- <span data-ttu-id="03889-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1786">'Identity'</span></span>
- <span data-ttu-id="03889-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1788">'Razor'</span></span>
- <span data-ttu-id="03889-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1791">'Blazor'</span></span>
- <span data-ttu-id="03889-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1792">'Identity'</span></span>
- <span data-ttu-id="03889-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1794">'Razor'</span></span>
- <span data-ttu-id="03889-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1797">'Blazor'</span></span>
- <span data-ttu-id="03889-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1798">'Identity'</span></span>
- <span data-ttu-id="03889-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1800">'Razor'</span></span>
- <span data-ttu-id="03889-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1803">'Blazor'</span></span>
- <span data-ttu-id="03889-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1804">'Identity'</span></span>
- <span data-ttu-id="03889-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1806">'Razor'</span></span>
- <span data-ttu-id="03889-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1809">'Blazor'</span></span>
- <span data-ttu-id="03889-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1810">'Identity'</span></span>
- <span data-ttu-id="03889-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1812">'Razor'</span></span>
- <span data-ttu-id="03889-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1815">'Blazor'</span></span>
- <span data-ttu-id="03889-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1816">'Identity'</span></span>
- <span data-ttu-id="03889-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1818">'Razor'</span></span>
- <span data-ttu-id="03889-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1821">'Blazor'</span></span>
- <span data-ttu-id="03889-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1822">'Identity'</span></span>
- <span data-ttu-id="03889-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1824">'Razor'</span></span>
- <span data-ttu-id="03889-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1827">'Blazor'</span></span>
- <span data-ttu-id="03889-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1828">'Identity'</span></span>
- <span data-ttu-id="03889-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1830">'Razor'</span></span>
- <span data-ttu-id="03889-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1833">'Blazor'</span></span>
- <span data-ttu-id="03889-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1834">'Identity'</span></span>
- <span data-ttu-id="03889-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1836">'Razor'</span></span>
- <span data-ttu-id="03889-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1839">'Blazor'</span></span>
- <span data-ttu-id="03889-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1840">'Identity'</span></span>
- <span data-ttu-id="03889-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1842">'Razor'</span></span>
- <span data-ttu-id="03889-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1845">'Blazor'</span></span>
- <span data-ttu-id="03889-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1846">'Identity'</span></span>
- <span data-ttu-id="03889-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1848">'Razor'</span></span>
- <span data-ttu-id="03889-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1851">'Blazor'</span></span>
- <span data-ttu-id="03889-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1852">'Identity'</span></span>
- <span data-ttu-id="03889-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1854">'Razor'</span></span>
- <span data-ttu-id="03889-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1857">'Blazor'</span></span>
- <span data-ttu-id="03889-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1858">'Identity'</span></span>
- <span data-ttu-id="03889-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1860">'Razor'</span></span>
- <span data-ttu-id="03889-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1863">'Blazor'</span></span>
- <span data-ttu-id="03889-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1864">'Identity'</span></span>
- <span data-ttu-id="03889-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1866">'Razor'</span></span>
- <span data-ttu-id="03889-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1869">'Blazor'</span></span>
- <span data-ttu-id="03889-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1870">'Identity'</span></span>
- <span data-ttu-id="03889-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1872">'Razor'</span></span>
- <span data-ttu-id="03889-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1875">'Blazor'</span></span>
- <span data-ttu-id="03889-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1876">'Identity'</span></span>
- <span data-ttu-id="03889-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1878">'Razor'</span></span>
- <span data-ttu-id="03889-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1881">'Blazor'</span></span>
- <span data-ttu-id="03889-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1882">'Identity'</span></span>
- <span data-ttu-id="03889-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1884">'Razor'</span></span>
- <span data-ttu-id="03889-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1887">'Blazor'</span></span>
- <span data-ttu-id="03889-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1888">'Identity'</span></span>
- <span data-ttu-id="03889-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1890">'Razor'</span></span>
- <span data-ttu-id="03889-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1893">'Blazor'</span></span>
- <span data-ttu-id="03889-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1894">'Identity'</span></span>
- <span data-ttu-id="03889-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1896">'Razor'</span></span>
- <span data-ttu-id="03889-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1899">'Blazor'</span></span>
- <span data-ttu-id="03889-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1900">'Identity'</span></span>
- <span data-ttu-id="03889-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1902">'Razor'</span></span>
- <span data-ttu-id="03889-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1905">'Blazor'</span></span>
- <span data-ttu-id="03889-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1906">'Identity'</span></span>
- <span data-ttu-id="03889-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1908">'Razor'</span></span>
- <span data-ttu-id="03889-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1911">'Blazor'</span></span>
- <span data-ttu-id="03889-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1912">'Identity'</span></span>
- <span data-ttu-id="03889-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1914">'Razor'</span></span>
- <span data-ttu-id="03889-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1917">'Blazor'</span></span>
- <span data-ttu-id="03889-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1918">'Identity'</span></span>
- <span data-ttu-id="03889-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1920">'Razor'</span></span>
- <span data-ttu-id="03889-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1923">'Blazor'</span></span>
- <span data-ttu-id="03889-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1924">'Identity'</span></span>
- <span data-ttu-id="03889-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1926">'Razor'</span></span>
- <span data-ttu-id="03889-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1929">'Blazor'</span></span>
- <span data-ttu-id="03889-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1930">'Identity'</span></span>
- <span data-ttu-id="03889-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1932">'Razor'</span></span>
- <span data-ttu-id="03889-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1935">'Blazor'</span></span>
- <span data-ttu-id="03889-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1936">'Identity'</span></span>
- <span data-ttu-id="03889-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1938">'Razor'</span></span>
- <span data-ttu-id="03889-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1941">'Blazor'</span></span>
- <span data-ttu-id="03889-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1942">'Identity'</span></span>
- <span data-ttu-id="03889-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1944">'Razor'</span></span>
- <span data-ttu-id="03889-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1947">'Blazor'</span></span>
- <span data-ttu-id="03889-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1948">'Identity'</span></span>
- <span data-ttu-id="03889-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1950">'Razor'</span></span>
- <span data-ttu-id="03889-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1953">'Blazor'</span></span>
- <span data-ttu-id="03889-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1954">'Identity'</span></span>
- <span data-ttu-id="03889-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1956">'Razor'</span></span>
- <span data-ttu-id="03889-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1959">'Blazor'</span></span>
- <span data-ttu-id="03889-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1960">'Identity'</span></span>
- <span data-ttu-id="03889-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1962">'Razor'</span></span>
- <span data-ttu-id="03889-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1965">'Blazor'</span></span>
- <span data-ttu-id="03889-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1966">'Identity'</span></span>
- <span data-ttu-id="03889-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1968">'Razor'</span></span>
- <span data-ttu-id="03889-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1971">'Blazor'</span></span>
- <span data-ttu-id="03889-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1972">'Identity'</span></span>
- <span data-ttu-id="03889-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1974">'Razor'</span></span>
- <span data-ttu-id="03889-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1977">'Blazor'</span></span>
- <span data-ttu-id="03889-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1978">'Identity'</span></span>
- <span data-ttu-id="03889-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1980">'Razor'</span></span>
- <span data-ttu-id="03889-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1983">'Blazor'</span></span>
- <span data-ttu-id="03889-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1984">'Identity'</span></span>
- <span data-ttu-id="03889-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1986">'Razor'</span></span>
- <span data-ttu-id="03889-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1989">'Blazor'</span></span>
- <span data-ttu-id="03889-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1990">'Identity'</span></span>
- <span data-ttu-id="03889-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1992">'Razor'</span></span>
- <span data-ttu-id="03889-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-1995">'Blazor'</span></span>
- <span data-ttu-id="03889-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-1996">'Identity'</span></span>
- <span data-ttu-id="03889-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-1998">'Razor'</span></span>
- <span data-ttu-id="03889-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2001">'Blazor'</span></span>
- <span data-ttu-id="03889-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2002">'Identity'</span></span>
- <span data-ttu-id="03889-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2004">'Razor'</span></span>
- <span data-ttu-id="03889-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2007">'Blazor'</span></span>
- <span data-ttu-id="03889-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2008">'Identity'</span></span>
- <span data-ttu-id="03889-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2010">'Razor'</span></span>
- <span data-ttu-id="03889-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2013">'Blazor'</span></span>
- <span data-ttu-id="03889-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2014">'Identity'</span></span>
- <span data-ttu-id="03889-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2016">'Razor'</span></span>
- <span data-ttu-id="03889-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2019">'Blazor'</span></span>
- <span data-ttu-id="03889-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2020">'Identity'</span></span>
- <span data-ttu-id="03889-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2022">'Razor'</span></span>
- <span data-ttu-id="03889-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2025">'Blazor'</span></span>
- <span data-ttu-id="03889-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2026">'Identity'</span></span>
- <span data-ttu-id="03889-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2028">'Razor'</span></span>
- <span data-ttu-id="03889-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="03889-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="03889-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-2032">値: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-2033">値: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="03889-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="03889-2034">値: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="03889-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="03889-2035">**例**</span><span class="sxs-lookup"><span data-stu-id="03889-2035">**Example**</span></span>

<span data-ttu-id="03889-2036">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="03889-2037">名前 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="03889-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="03889-2038">数値 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="03889-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="03889-2039">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="03889-2040">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2040">File Configuration Provider</span></span>

<span data-ttu-id="03889-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="03889-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="03889-2042">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="03889-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="03889-2043">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="03889-2044">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="03889-2045">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="03889-2046">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2046">INI Configuration Provider</span></span>

<span data-ttu-id="03889-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-2048">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03889-2049">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="03889-2050">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="03889-2051">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="03889-2052">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03889-2053">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03889-2054">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03889-2055">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="03889-2055">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="03889-2056">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03889-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2057">section0:key0</span></span>
* <span data-ttu-id="03889-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2058">section0:key1</span></span>
* <span data-ttu-id="03889-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="03889-2059">section1:subsection:key</span></span>
* <span data-ttu-id="03889-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="03889-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="03889-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="03889-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="03889-2062">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="03889-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="03889-2064">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03889-2065">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="03889-2066">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="03889-2067">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03889-2068">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03889-2069">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="03889-2070">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="03889-2071">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="03889-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="03889-2072">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="03889-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="03889-2073">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="03889-2074">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="03889-2075">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="03889-2076">環境変数。</span><span class="sxs-lookup"><span data-stu-id="03889-2076">Environment variables.</span></span>
* <span data-ttu-id="03889-2077">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="03889-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="03889-2078">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="03889-2078">Command-line arguments.</span></span>

<span data-ttu-id="03889-2079">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="03889-2080">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="03889-2081">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03889-2082">**例**</span><span class="sxs-lookup"><span data-stu-id="03889-2082">**Example**</span></span>

<span data-ttu-id="03889-2083">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="03889-2084">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="03889-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="03889-2085">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="03889-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="03889-2086">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="03889-2087">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="03889-2087">Run the sample app.</span></span> <span data-ttu-id="03889-2088">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="03889-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="03889-2089">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="03889-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="03889-2090">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="03889-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="03889-2091">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="03889-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="03889-2092">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="03889-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="03889-2093">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="03889-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="03889-2094">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="03889-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="03889-2095">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="03889-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="03889-2096">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="03889-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="03889-2097">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2097">XML Configuration Provider</span></span>

<span data-ttu-id="03889-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="03889-2099">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03889-2100">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="03889-2101">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="03889-2102">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="03889-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="03889-2103">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="03889-2104">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="03889-2105">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="03889-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="03889-2106">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="03889-2107">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="03889-2108">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03889-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2109">section0:key0</span></span>
* <span data-ttu-id="03889-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2110">section0:key1</span></span>
* <span data-ttu-id="03889-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2111">section1:key0</span></span>
* <span data-ttu-id="03889-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2112">section1:key1</span></span>

<span data-ttu-id="03889-2113">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="03889-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="03889-2114">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03889-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="03889-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="03889-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="03889-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2118">section:section1:key:key1</span></span>

<span data-ttu-id="03889-2119">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="03889-2120">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="03889-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="03889-2121">key:attribute</span></span>
* <span data-ttu-id="03889-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="03889-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="03889-2123">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="03889-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="03889-2125">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="03889-2125">The key is the file name.</span></span> <span data-ttu-id="03889-2126">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2126">The value contains the file's contents.</span></span> <span data-ttu-id="03889-2127">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="03889-2128">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="03889-2129">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="03889-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="03889-2130">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="03889-2131">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="03889-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="03889-2132">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="03889-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="03889-2133">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="03889-2134">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="03889-2135">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="03889-2136">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="03889-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="03889-2138">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="03889-2139">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="03889-2140">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="03889-2141">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="03889-2142">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="03889-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="03889-2143">GetValue</span></span>

<span data-ttu-id="03889-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="03889-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="03889-2145">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="03889-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="03889-2146">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="03889-2146">The following example:</span></span>

* <span data-ttu-id="03889-2147">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="03889-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="03889-2148">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="03889-2149">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="03889-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="03889-2150">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="03889-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="03889-2151">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="03889-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="03889-2152">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="03889-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="03889-2153">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="03889-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="03889-2154">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="03889-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2155">section0:key0</span></span>
* <span data-ttu-id="03889-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2156">section0:key1</span></span>
* <span data-ttu-id="03889-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2157">section1:key0</span></span>
* <span data-ttu-id="03889-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2158">section1:key1</span></span>
* <span data-ttu-id="03889-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="03889-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="03889-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="03889-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="03889-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="03889-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="03889-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="03889-2163">GetSection</span></span>

<span data-ttu-id="03889-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="03889-2165">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="03889-2166">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="03889-2167">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="03889-2168">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="03889-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="03889-2169">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="03889-2170">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="03889-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="03889-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="03889-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="03889-2172">GetChildren</span></span>

<span data-ttu-id="03889-2173">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="03889-2174">既存</span><span class="sxs-lookup"><span data-stu-id="03889-2174">Exists</span></span>

<span data-ttu-id="03889-2175">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="03889-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="03889-2176">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="03889-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="03889-2177">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="03889-2177">Bind to an object graph</span></span>

<span data-ttu-id="03889-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="03889-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="03889-2179">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="03889-2180">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="03889-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="03889-2181">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="03889-2182">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="03889-2183">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="03889-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="03889-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="03889-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="03889-2185">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="03889-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="03889-2186">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="03889-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="03889-2187">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="03889-2187">Bind an array to a class</span></span>

<span data-ttu-id="03889-2188">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="03889-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="03889-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="03889-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="03889-2190">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="03889-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="03889-2191">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2191">Binding is provided by convention.</span></span> <span data-ttu-id="03889-2192">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="03889-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="03889-2193">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="03889-2193">**In-memory array processing**</span></span>

<span data-ttu-id="03889-2194">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="03889-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="03889-2195">Key</span><span class="sxs-lookup"><span data-stu-id="03889-2195">Key</span></span>             | <span data-ttu-id="03889-2196">[値]</span><span class="sxs-lookup"><span data-stu-id="03889-2196">Value</span></span>  |
| :---
<span data-ttu-id="03889-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2198">'Blazor'</span></span>
- <span data-ttu-id="03889-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2199">'Identity'</span></span>
- <span data-ttu-id="03889-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2201">'Razor'</span></span>
- <span data-ttu-id="03889-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2204">'Blazor'</span></span>
- <span data-ttu-id="03889-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2205">'Identity'</span></span>
- <span data-ttu-id="03889-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2207">'Razor'</span></span>
- <span data-ttu-id="03889-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2210">'Blazor'</span></span>
- <span data-ttu-id="03889-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2211">'Identity'</span></span>
- <span data-ttu-id="03889-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2213">'Razor'</span></span>
- <span data-ttu-id="03889-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2216">'Blazor'</span></span>
- <span data-ttu-id="03889-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2217">'Identity'</span></span>
- <span data-ttu-id="03889-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2219">'Razor'</span></span>
- <span data-ttu-id="03889-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="03889-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="03889-2222">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="03889-2223">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="03889-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="03889-2224">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="03889-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="03889-2225">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="03889-2226">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="03889-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="03889-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="03889-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="03889-2228">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="03889-2229">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="03889-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="03889-2230">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="03889-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="03889-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2232">'Blazor'</span></span>
- <span data-ttu-id="03889-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2233">'Identity'</span></span>
- <span data-ttu-id="03889-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2235">'Razor'</span></span>
- <span data-ttu-id="03889-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2238">'Blazor'</span></span>
- <span data-ttu-id="03889-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2239">'Identity'</span></span>
- <span data-ttu-id="03889-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2241">'Razor'</span></span>
- <span data-ttu-id="03889-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2244">'Blazor'</span></span>
- <span data-ttu-id="03889-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2245">'Identity'</span></span>
- <span data-ttu-id="03889-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2247">'Razor'</span></span>
- <span data-ttu-id="03889-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2250">'Blazor'</span></span>
- <span data-ttu-id="03889-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2251">'Identity'</span></span>
- <span data-ttu-id="03889-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2253">'Razor'</span></span>
- <span data-ttu-id="03889-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2256">'Blazor'</span></span>
- <span data-ttu-id="03889-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2257">'Identity'</span></span>
- <span data-ttu-id="03889-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2259">'Razor'</span></span>
- <span data-ttu-id="03889-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2262">'Blazor'</span></span>
- <span data-ttu-id="03889-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2263">'Identity'</span></span>
- <span data-ttu-id="03889-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2265">'Razor'</span></span>
- <span data-ttu-id="03889-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2268">'Blazor'</span></span>
- <span data-ttu-id="03889-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2269">'Identity'</span></span>
- <span data-ttu-id="03889-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2271">'Razor'</span></span>
- <span data-ttu-id="03889-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2274">'Blazor'</span></span>
- <span data-ttu-id="03889-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2275">'Identity'</span></span>
- <span data-ttu-id="03889-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2277">'Razor'</span></span>
- <span data-ttu-id="03889-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2280">'Blazor'</span></span>
- <span data-ttu-id="03889-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2281">'Identity'</span></span>
- <span data-ttu-id="03889-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2283">'Razor'</span></span>
- <span data-ttu-id="03889-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2286">'Blazor'</span></span>
- <span data-ttu-id="03889-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2287">'Identity'</span></span>
- <span data-ttu-id="03889-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2289">'Razor'</span></span>
- <span data-ttu-id="03889-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2292">'Blazor'</span></span>
- <span data-ttu-id="03889-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2293">'Identity'</span></span>
- <span data-ttu-id="03889-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2295">'Razor'</span></span>
- <span data-ttu-id="03889-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2298">'Blazor'</span></span>
- <span data-ttu-id="03889-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2299">'Identity'</span></span>
- <span data-ttu-id="03889-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2301">'Razor'</span></span>
- <span data-ttu-id="03889-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2304">'Blazor'</span></span>
- <span data-ttu-id="03889-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2305">'Identity'</span></span>
- <span data-ttu-id="03889-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2307">'Razor'</span></span>
- <span data-ttu-id="03889-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2310">'Blazor'</span></span>
- <span data-ttu-id="03889-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2311">'Identity'</span></span>
- <span data-ttu-id="03889-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2313">'Razor'</span></span>
- <span data-ttu-id="03889-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2316">'Blazor'</span></span>
- <span data-ttu-id="03889-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2317">'Identity'</span></span>
- <span data-ttu-id="03889-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2319">'Razor'</span></span>
- <span data-ttu-id="03889-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2322">'Blazor'</span></span>
- <span data-ttu-id="03889-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2323">'Identity'</span></span>
- <span data-ttu-id="03889-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2325">'Razor'</span></span>
- <span data-ttu-id="03889-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2328">'Blazor'</span></span>
- <span data-ttu-id="03889-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2329">'Identity'</span></span>
- <span data-ttu-id="03889-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2331">'Razor'</span></span>
- <span data-ttu-id="03889-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2334">'Blazor'</span></span>
- <span data-ttu-id="03889-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2335">'Identity'</span></span>
- <span data-ttu-id="03889-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2337">'Razor'</span></span>
- <span data-ttu-id="03889-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2340">'Blazor'</span></span>
- <span data-ttu-id="03889-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2341">'Identity'</span></span>
- <span data-ttu-id="03889-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2343">'Razor'</span></span>
- <span data-ttu-id="03889-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2346">'Blazor'</span></span>
- <span data-ttu-id="03889-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2347">'Identity'</span></span>
- <span data-ttu-id="03889-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2349">'Razor'</span></span>
- <span data-ttu-id="03889-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2352">'Blazor'</span></span>
- <span data-ttu-id="03889-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2353">'Identity'</span></span>
- <span data-ttu-id="03889-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2355">'Razor'</span></span>
- <span data-ttu-id="03889-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2358">'Blazor'</span></span>
- <span data-ttu-id="03889-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2359">'Identity'</span></span>
- <span data-ttu-id="03889-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2361">'Razor'</span></span>
- <span data-ttu-id="03889-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="03889-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="03889-2364">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="03889-2365">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="03889-2366">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="03889-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="03889-2367">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="03889-2368">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="03889-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="03889-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="03889-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="03889-2370">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="03889-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="03889-2371">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="03889-2372">Key</span><span class="sxs-lookup"><span data-stu-id="03889-2372">Key</span></span>             | <span data-ttu-id="03889-2373">[値]</span><span class="sxs-lookup"><span data-stu-id="03889-2373">Value</span></span>  |
| :---
<span data-ttu-id="03889-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2375">'Blazor'</span></span>
- <span data-ttu-id="03889-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2376">'Identity'</span></span>
- <span data-ttu-id="03889-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2378">'Razor'</span></span>
- <span data-ttu-id="03889-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2381">'Blazor'</span></span>
- <span data-ttu-id="03889-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2382">'Identity'</span></span>
- <span data-ttu-id="03889-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2384">'Razor'</span></span>
- <span data-ttu-id="03889-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2387">'Blazor'</span></span>
- <span data-ttu-id="03889-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2388">'Identity'</span></span>
- <span data-ttu-id="03889-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2390">'Razor'</span></span>
- <span data-ttu-id="03889-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2393">'Blazor'</span></span>
- <span data-ttu-id="03889-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2394">'Identity'</span></span>
- <span data-ttu-id="03889-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2396">'Razor'</span></span>
- <span data-ttu-id="03889-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="03889-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="03889-2399">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="03889-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="03889-2400">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="03889-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="03889-2401">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="03889-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="03889-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2403">'Blazor'</span></span>
- <span data-ttu-id="03889-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2404">'Identity'</span></span>
- <span data-ttu-id="03889-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2406">'Razor'</span></span>
- <span data-ttu-id="03889-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2409">'Blazor'</span></span>
- <span data-ttu-id="03889-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2410">'Identity'</span></span>
- <span data-ttu-id="03889-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2412">'Razor'</span></span>
- <span data-ttu-id="03889-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2415">'Blazor'</span></span>
- <span data-ttu-id="03889-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2416">'Identity'</span></span>
- <span data-ttu-id="03889-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2418">'Razor'</span></span>
- <span data-ttu-id="03889-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2421">'Blazor'</span></span>
- <span data-ttu-id="03889-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2422">'Identity'</span></span>
- <span data-ttu-id="03889-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2424">'Razor'</span></span>
- <span data-ttu-id="03889-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2427">'Blazor'</span></span>
- <span data-ttu-id="03889-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2428">'Identity'</span></span>
- <span data-ttu-id="03889-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2430">'Razor'</span></span>
- <span data-ttu-id="03889-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2433">'Blazor'</span></span>
- <span data-ttu-id="03889-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2434">'Identity'</span></span>
- <span data-ttu-id="03889-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2436">'Razor'</span></span>
- <span data-ttu-id="03889-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2439">'Blazor'</span></span>
- <span data-ttu-id="03889-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2440">'Identity'</span></span>
- <span data-ttu-id="03889-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2442">'Razor'</span></span>
- <span data-ttu-id="03889-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2445">'Blazor'</span></span>
- <span data-ttu-id="03889-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2446">'Identity'</span></span>
- <span data-ttu-id="03889-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2448">'Razor'</span></span>
- <span data-ttu-id="03889-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2451">'Blazor'</span></span>
- <span data-ttu-id="03889-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2452">'Identity'</span></span>
- <span data-ttu-id="03889-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2454">'Razor'</span></span>
- <span data-ttu-id="03889-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2457">'Blazor'</span></span>
- <span data-ttu-id="03889-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2458">'Identity'</span></span>
- <span data-ttu-id="03889-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2460">'Razor'</span></span>
- <span data-ttu-id="03889-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2463">'Blazor'</span></span>
- <span data-ttu-id="03889-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2464">'Identity'</span></span>
- <span data-ttu-id="03889-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2466">'Razor'</span></span>
- <span data-ttu-id="03889-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2469">'Blazor'</span></span>
- <span data-ttu-id="03889-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2470">'Identity'</span></span>
- <span data-ttu-id="03889-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2472">'Razor'</span></span>
- <span data-ttu-id="03889-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2475">'Blazor'</span></span>
- <span data-ttu-id="03889-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2476">'Identity'</span></span>
- <span data-ttu-id="03889-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2478">'Razor'</span></span>
- <span data-ttu-id="03889-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2481">'Blazor'</span></span>
- <span data-ttu-id="03889-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2482">'Identity'</span></span>
- <span data-ttu-id="03889-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2484">'Razor'</span></span>
- <span data-ttu-id="03889-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2487">'Blazor'</span></span>
- <span data-ttu-id="03889-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2488">'Identity'</span></span>
- <span data-ttu-id="03889-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2490">'Razor'</span></span>
- <span data-ttu-id="03889-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2493">'Blazor'</span></span>
- <span data-ttu-id="03889-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2494">'Identity'</span></span>
- <span data-ttu-id="03889-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2496">'Razor'</span></span>
- <span data-ttu-id="03889-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2499">'Blazor'</span></span>
- <span data-ttu-id="03889-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2500">'Identity'</span></span>
- <span data-ttu-id="03889-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2502">'Razor'</span></span>
- <span data-ttu-id="03889-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2505">'Blazor'</span></span>
- <span data-ttu-id="03889-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2506">'Identity'</span></span>
- <span data-ttu-id="03889-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2508">'Razor'</span></span>
- <span data-ttu-id="03889-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2511">'Blazor'</span></span>
- <span data-ttu-id="03889-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2512">'Identity'</span></span>
- <span data-ttu-id="03889-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2514">'Razor'</span></span>
- <span data-ttu-id="03889-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2517">'Blazor'</span></span>
- <span data-ttu-id="03889-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2518">'Identity'</span></span>
- <span data-ttu-id="03889-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2520">'Razor'</span></span>
- <span data-ttu-id="03889-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2523">'Blazor'</span></span>
- <span data-ttu-id="03889-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2524">'Identity'</span></span>
- <span data-ttu-id="03889-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2526">'Razor'</span></span>
- <span data-ttu-id="03889-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2529">'Blazor'</span></span>
- <span data-ttu-id="03889-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2530">'Identity'</span></span>
- <span data-ttu-id="03889-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2532">'Razor'</span></span>
- <span data-ttu-id="03889-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="03889-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="03889-2535">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="03889-2535">**JSON array processing**</span></span>

<span data-ttu-id="03889-2536">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="03889-2537">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="03889-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="03889-2538">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="03889-2539">Key</span><span class="sxs-lookup"><span data-stu-id="03889-2539">Key</span></span>                     | <span data-ttu-id="03889-2540">[値]</span><span class="sxs-lookup"><span data-stu-id="03889-2540">Value</span></span>  |
| ---
<span data-ttu-id="03889-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2542">'Blazor'</span></span>
- <span data-ttu-id="03889-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2543">'Identity'</span></span>
- <span data-ttu-id="03889-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2545">'Razor'</span></span>
- <span data-ttu-id="03889-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2548">'Blazor'</span></span>
- <span data-ttu-id="03889-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2549">'Identity'</span></span>
- <span data-ttu-id="03889-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2551">'Razor'</span></span>
- <span data-ttu-id="03889-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2554">'Blazor'</span></span>
- <span data-ttu-id="03889-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2555">'Identity'</span></span>
- <span data-ttu-id="03889-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2557">'Razor'</span></span>
- <span data-ttu-id="03889-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2560">'Blazor'</span></span>
- <span data-ttu-id="03889-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2561">'Identity'</span></span>
- <span data-ttu-id="03889-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2563">'Razor'</span></span>
- <span data-ttu-id="03889-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2566">'Blazor'</span></span>
- <span data-ttu-id="03889-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2567">'Identity'</span></span>
- <span data-ttu-id="03889-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2569">'Razor'</span></span>
- <span data-ttu-id="03889-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2572">'Blazor'</span></span>
- <span data-ttu-id="03889-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2573">'Identity'</span></span>
- <span data-ttu-id="03889-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2575">'Razor'</span></span>
- <span data-ttu-id="03889-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2578">'Blazor'</span></span>
- <span data-ttu-id="03889-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2579">'Identity'</span></span>
- <span data-ttu-id="03889-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2581">'Razor'</span></span>
- <span data-ttu-id="03889-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2584">'Blazor'</span></span>
- <span data-ttu-id="03889-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2585">'Identity'</span></span>
- <span data-ttu-id="03889-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2587">'Razor'</span></span>
- <span data-ttu-id="03889-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2590">'Blazor'</span></span>
- <span data-ttu-id="03889-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2591">'Identity'</span></span>
- <span data-ttu-id="03889-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2593">'Razor'</span></span>
- <span data-ttu-id="03889-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="03889-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="03889-2596">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="03889-2597">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="03889-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="03889-2598">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="03889-2599">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="03889-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="03889-2600">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="03889-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="03889-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2602">'Blazor'</span></span>
- <span data-ttu-id="03889-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2603">'Identity'</span></span>
- <span data-ttu-id="03889-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2605">'Razor'</span></span>
- <span data-ttu-id="03889-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2608">'Blazor'</span></span>
- <span data-ttu-id="03889-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2609">'Identity'</span></span>
- <span data-ttu-id="03889-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2611">'Razor'</span></span>
- <span data-ttu-id="03889-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2614">'Blazor'</span></span>
- <span data-ttu-id="03889-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2615">'Identity'</span></span>
- <span data-ttu-id="03889-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2617">'Razor'</span></span>
- <span data-ttu-id="03889-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2620">'Blazor'</span></span>
- <span data-ttu-id="03889-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2621">'Identity'</span></span>
- <span data-ttu-id="03889-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2623">'Razor'</span></span>
- <span data-ttu-id="03889-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2626">'Blazor'</span></span>
- <span data-ttu-id="03889-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2627">'Identity'</span></span>
- <span data-ttu-id="03889-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2629">'Razor'</span></span>
- <span data-ttu-id="03889-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2632">'Blazor'</span></span>
- <span data-ttu-id="03889-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2633">'Identity'</span></span>
- <span data-ttu-id="03889-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2635">'Razor'</span></span>
- <span data-ttu-id="03889-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2638">'Blazor'</span></span>
- <span data-ttu-id="03889-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2639">'Identity'</span></span>
- <span data-ttu-id="03889-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2641">'Razor'</span></span>
- <span data-ttu-id="03889-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2644">'Blazor'</span></span>
- <span data-ttu-id="03889-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2645">'Identity'</span></span>
- <span data-ttu-id="03889-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2647">'Razor'</span></span>
- <span data-ttu-id="03889-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2650">'Blazor'</span></span>
- <span data-ttu-id="03889-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2651">'Identity'</span></span>
- <span data-ttu-id="03889-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2653">'Razor'</span></span>
- <span data-ttu-id="03889-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2656">'Blazor'</span></span>
- <span data-ttu-id="03889-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2657">'Identity'</span></span>
- <span data-ttu-id="03889-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2659">'Razor'</span></span>
- <span data-ttu-id="03889-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2662">'Blazor'</span></span>
- <span data-ttu-id="03889-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2663">'Identity'</span></span>
- <span data-ttu-id="03889-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2665">'Razor'</span></span>
- <span data-ttu-id="03889-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2668">'Blazor'</span></span>
- <span data-ttu-id="03889-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2669">'Identity'</span></span>
- <span data-ttu-id="03889-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2671">'Razor'</span></span>
- <span data-ttu-id="03889-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2674">'Blazor'</span></span>
- <span data-ttu-id="03889-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2675">'Identity'</span></span>
- <span data-ttu-id="03889-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2677">'Razor'</span></span>
- <span data-ttu-id="03889-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2680">'Blazor'</span></span>
- <span data-ttu-id="03889-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2681">'Identity'</span></span>
- <span data-ttu-id="03889-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2683">'Razor'</span></span>
- <span data-ttu-id="03889-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2686">'Blazor'</span></span>
- <span data-ttu-id="03889-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2687">'Identity'</span></span>
- <span data-ttu-id="03889-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2689">'Razor'</span></span>
- <span data-ttu-id="03889-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2692">'Blazor'</span></span>
- <span data-ttu-id="03889-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2693">'Identity'</span></span>
- <span data-ttu-id="03889-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2695">'Razor'</span></span>
- <span data-ttu-id="03889-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2698">'Blazor'</span></span>
- <span data-ttu-id="03889-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2699">'Identity'</span></span>
- <span data-ttu-id="03889-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2701">'Razor'</span></span>
- <span data-ttu-id="03889-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2704">'Blazor'</span></span>
- <span data-ttu-id="03889-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2705">'Identity'</span></span>
- <span data-ttu-id="03889-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2707">'Razor'</span></span>
- <span data-ttu-id="03889-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2710">'Blazor'</span></span>
- <span data-ttu-id="03889-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2711">'Identity'</span></span>
- <span data-ttu-id="03889-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2713">'Razor'</span></span>
- <span data-ttu-id="03889-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2716">'Blazor'</span></span>
- <span data-ttu-id="03889-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2717">'Identity'</span></span>
- <span data-ttu-id="03889-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2719">'Razor'</span></span>
- <span data-ttu-id="03889-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2722">'Blazor'</span></span>
- <span data-ttu-id="03889-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2723">'Identity'</span></span>
- <span data-ttu-id="03889-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2725">'Razor'</span></span>
- <span data-ttu-id="03889-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2728">'Blazor'</span></span>
- <span data-ttu-id="03889-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2729">'Identity'</span></span>
- <span data-ttu-id="03889-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2731">'Razor'</span></span>
- <span data-ttu-id="03889-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2734">'Blazor'</span></span>
- <span data-ttu-id="03889-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2735">'Identity'</span></span>
- <span data-ttu-id="03889-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2737">'Razor'</span></span>
- <span data-ttu-id="03889-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2740">'Blazor'</span></span>
- <span data-ttu-id="03889-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2741">'Identity'</span></span>
- <span data-ttu-id="03889-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2743">'Razor'</span></span>
- <span data-ttu-id="03889-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2746">'Blazor'</span></span>
- <span data-ttu-id="03889-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2747">'Identity'</span></span>
- <span data-ttu-id="03889-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2749">'Razor'</span></span>
- <span data-ttu-id="03889-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2752">'Blazor'</span></span>
- <span data-ttu-id="03889-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2753">'Identity'</span></span>
- <span data-ttu-id="03889-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2755">'Razor'</span></span>
- <span data-ttu-id="03889-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2758">'Blazor'</span></span>
- <span data-ttu-id="03889-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2759">'Identity'</span></span>
- <span data-ttu-id="03889-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2761">'Razor'</span></span>
- <span data-ttu-id="03889-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03889-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="03889-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03889-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03889-2764">'Blazor'</span></span>
- <span data-ttu-id="03889-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03889-2765">'Identity'</span></span>
- <span data-ttu-id="03889-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03889-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="03889-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03889-2767">'Razor'</span></span>
- <span data-ttu-id="03889-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="03889-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="03889-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="03889-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="03889-2770">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="03889-2770">Custom configuration provider</span></span>

<span data-ttu-id="03889-2771">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="03889-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="03889-2772">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="03889-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="03889-2773">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="03889-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="03889-2774">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="03889-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="03889-2775">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="03889-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="03889-2776">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="03889-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="03889-2777">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="03889-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="03889-2778">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="03889-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="03889-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="03889-2780">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="03889-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="03889-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="03889-2782"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="03889-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="03889-2784"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="03889-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="03889-2785">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="03889-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="03889-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="03889-2787">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="03889-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="03889-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="03889-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="03889-2789">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="03889-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="03889-2790">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="03889-2790">Access configuration during startup</span></span>

<span data-ttu-id="03889-2791">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="03889-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="03889-2792">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="03889-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="03889-2793">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03889-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="03889-2794">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="03889-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="03889-2795">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="03889-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="03889-2796">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="03889-2796">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="03889-2797">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="03889-2797">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="03889-2798">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="03889-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="03889-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="03889-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="03889-2800">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03889-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03889-2801">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="03889-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
