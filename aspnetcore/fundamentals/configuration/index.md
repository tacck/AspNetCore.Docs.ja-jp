---
<span data-ttu-id="8c301-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-102">'Blazor'</span></span>
- <span data-ttu-id="8c301-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-103">'Identity'</span></span>
- <span data-ttu-id="8c301-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-105">'Razor'</span></span>
- <span data-ttu-id="8c301-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8c301-107">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="8c301-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8c301-108">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8c301-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c301-109">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8c301-110">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="8c301-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8c301-111">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="8c301-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8c301-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="8c301-112">Environment variables</span></span>
* <span data-ttu-id="8c301-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8c301-113">Azure Key Vault</span></span>
* <span data-ttu-id="8c301-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8c301-114">Azure App Configuration</span></span>
* <span data-ttu-id="8c301-115">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8c301-115">Command-line arguments</span></span>
* <span data-ttu-id="8c301-116">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="8c301-117">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8c301-117">Directory files</span></span>
* <span data-ttu-id="8c301-118">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="8c301-118">In-memory .NET objects</span></span>

<span data-ttu-id="8c301-119">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8c301-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8c301-120">既定の構成</span><span class="sxs-lookup"><span data-stu-id="8c301-120">Default configuration</span></span>

<span data-ttu-id="8c301-121">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="8c301-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8c301-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8c301-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8c301-124">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8c301-125">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="8c301-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8c301-126">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="8c301-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8c301-127">たとえば、*appsettings*.***Production***.*json* および  *appsettings*.***Development***.*json*。</span><span class="sxs-lookup"><span data-stu-id="8c301-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="8c301-128">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8c301-129">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8c301-130">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8c301-131">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8c301-132">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8c301-133">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#command-line-configuration-provider) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="8c301-134">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8c301-135">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="8c301-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8c301-136">appsettings.json</span></span>

<span data-ttu-id="8c301-137">以下の *appsettings.json* ファイルについて考えます:</span><span class="sxs-lookup"><span data-stu-id="8c301-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8c301-138">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-139">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="8c301-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="8c301-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8c301-140">*appsettings.json*</span></span>
1. <span data-ttu-id="8c301-141">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production***.*json* および *appsettings*.***Development***.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="8c301-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="8c301-142">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8c301-143">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8c301-144">*appsettings*.`Environment`.*json* の値は、*appsettings. json*のキーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8c301-145">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="8c301-145">For example, by default:</span></span>

* <span data-ttu-id="8c301-146">開発においては、*appsettings*.***Development***.*json* 構成が *appsettings.json* の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="8c301-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8c301-147">運用環境では、*appsettings*.***Production***.*json* 構成が *appsettings. json*の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="8c301-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8c301-148">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="8c301-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8c301-149">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="8c301-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8c301-150">[既定の](#default)構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効化できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8c301-151">アプリの***開始後***に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8c301-152">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8c301-153">セキュリティとシークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="8c301-153">Security and secret manager</span></span>

<span data-ttu-id="8c301-154">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="8c301-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="8c301-155">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8c301-156">[シークレット マネージャー](xref:security/app-secrets) を使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8c301-157">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8c301-158">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8c301-159">[既定](#default)では、[シークレット マネージャー](xref:security/app-secrets)は*appsettings.json* と *appsettings.* `Environment` *.json* の後に構成設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8c301-160">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="8c301-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8c301-161"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8c301-162">シークレット マネージャーでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8c301-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8c301-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8c301-164">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8c301-165">環境変数</span><span class="sxs-lookup"><span data-stu-id="8c301-165">Environment variables</span></span>

<span data-ttu-id="8c301-166">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> は *appsettings.json*、*appsettings.* `Environment` *.json*、および [シークレット マネージャー](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8c301-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8c301-167">そのため、環境から読み取られたキー値は、*appsettings.json*、*appsettings.* `Environment` *.json*、シークレット マネージャーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8c301-168">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="8c301-168">The following `set` commands:</span></span>

* <span data-ttu-id="8c301-169">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8c301-170">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="8c301-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8c301-171">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8c301-172">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="8c301-172">The preceding environment settings:</span></span>

* <span data-ttu-id="8c301-173">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="8c301-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8c301-174">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="8c301-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8c301-175">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8c301-176">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8c301-177">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8c301-178">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8c301-179">上記のコマンドが *appsettings.json* と *appsettings.* `Environment` *.json* をオーバーライドすることをテストするには:</span><span class="sxs-lookup"><span data-stu-id="8c301-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8c301-180">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="8c301-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8c301-181">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="8c301-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8c301-182">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="8c301-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8c301-183">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8c301-183">In the preceding code:</span></span>

* <span data-ttu-id="8c301-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8c301-185">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8c301-186">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8c301-187">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8c301-188">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8c301-189">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="8c301-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8c301-190">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8c301-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8c301-191">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8c301-192">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8c301-193">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。</span><span class="sxs-lookup"><span data-stu-id="8c301-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8c301-194">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="8c301-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8c301-195">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8c301-196">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-196">Exposed as environment variables.</span></span>

<span data-ttu-id="8c301-197">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8c301-198">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8c301-199">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="8c301-199">Command-line</span></span>

<span data-ttu-id="8c301-200">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="8c301-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8c301-201">*appsettings.json* と *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="8c301-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8c301-202">開発環境の [App シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8c301-203">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-203">Environment variables.</span></span>

<span data-ttu-id="8c301-204">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8c301-205">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8c301-205">Command-line arguments</span></span>

<span data-ttu-id="8c301-206">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8c301-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8c301-207">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8c301-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8c301-208">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8c301-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8c301-209">キーの値:</span><span class="sxs-lookup"><span data-stu-id="8c301-209">The key value:</span></span>

* <span data-ttu-id="8c301-210">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8c301-211">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="8c301-212">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="8c301-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="8c301-213">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8c301-214">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="8c301-214">Switch mappings</span></span>

<span data-ttu-id="8c301-215">スイッチ マッピングでは、**キー**名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8c301-216"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c301-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8c301-217">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8c301-218">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8c301-219">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="8c301-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8c301-220">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="8c301-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8c301-221">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8c301-222">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8c301-223">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="8c301-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8c301-224">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="8c301-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-225">次のコマンドを実行して、キーの置換をテストします：</span><span class="sxs-lookup"><span data-stu-id="8c301-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8c301-226">メモ:現時点では、`=` を使用してシングルダッシュの `-` でキーの置換値を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="8c301-227">[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/3059)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="8c301-228">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="8c301-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8c301-229">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8c301-230">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c301-231">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="8c301-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8c301-232">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="8c301-232">Hierarchical configuration data</span></span>

<span data-ttu-id="8c301-233">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="8c301-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8c301-234">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *appsettings.json* 　ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8c301-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8c301-235">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8c301-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-236">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8c301-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8c301-237">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8c301-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8c301-239">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8c301-240">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="8c301-240">Configuration keys and values</span></span>

<span data-ttu-id="8c301-241">構成キー:</span><span class="sxs-lookup"><span data-stu-id="8c301-241">Configuration keys:</span></span>

* <span data-ttu-id="8c301-242">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-242">Are case-insensitive.</span></span> <span data-ttu-id="8c301-243">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8c301-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8c301-244">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8c301-245">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8c301-246">階層キー</span><span class="sxs-lookup"><span data-stu-id="8c301-246">Hierarchical keys</span></span>
  * <span data-ttu-id="8c301-247">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="8c301-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8c301-248">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8c301-249">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8c301-250">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c301-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8c301-251">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8c301-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8c301-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c301-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8c301-253">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8c301-254">構成値:</span><span class="sxs-lookup"><span data-stu-id="8c301-254">Configuration values:</span></span>

* <span data-ttu-id="8c301-255">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="8c301-255">Are strings.</span></span>
* <span data-ttu-id="8c301-256">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8c301-257">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-257">Configuration providers</span></span>

<span data-ttu-id="8c301-258">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8c301-259">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-259">Provider</span></span> | <span data-ttu-id="8c301-260">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="8c301-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="8c301-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-262">'Blazor'</span></span>
- <span data-ttu-id="8c301-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-263">'Identity'</span></span>
- <span data-ttu-id="8c301-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-265">'Razor'</span></span>
- <span data-ttu-id="8c301-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-268">'Blazor'</span></span>
- <span data-ttu-id="8c301-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-269">'Identity'</span></span>
- <span data-ttu-id="8c301-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-271">'Razor'</span></span>
- <span data-ttu-id="8c301-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-272">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-274">'Blazor'</span></span>
- <span data-ttu-id="8c301-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-275">'Identity'</span></span>
- <span data-ttu-id="8c301-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-277">'Razor'</span></span>
- <span data-ttu-id="8c301-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-280">'Blazor'</span></span>
- <span data-ttu-id="8c301-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-281">'Identity'</span></span>
- <span data-ttu-id="8c301-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-283">'Razor'</span></span>
- <span data-ttu-id="8c301-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-286">'Blazor'</span></span>
- <span data-ttu-id="8c301-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-287">'Identity'</span></span>
- <span data-ttu-id="8c301-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-289">'Razor'</span></span>
- <span data-ttu-id="8c301-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-292">'Blazor'</span></span>
- <span data-ttu-id="8c301-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-293">'Identity'</span></span>
- <span data-ttu-id="8c301-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-295">'Razor'</span></span>
- <span data-ttu-id="8c301-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-298">'Blazor'</span></span>
- <span data-ttu-id="8c301-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-299">'Identity'</span></span>
- <span data-ttu-id="8c301-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-301">'Razor'</span></span>
- <span data-ttu-id="8c301-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-304">'Blazor'</span></span>
- <span data-ttu-id="8c301-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-305">'Identity'</span></span>
- <span data-ttu-id="8c301-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-307">'Razor'</span></span>
- <span data-ttu-id="8c301-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-310">'Blazor'</span></span>
- <span data-ttu-id="8c301-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-311">'Identity'</span></span>
- <span data-ttu-id="8c301-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-313">'Razor'</span></span>
- <span data-ttu-id="8c301-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-316">'Blazor'</span></span>
- <span data-ttu-id="8c301-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-317">'Identity'</span></span>
- <span data-ttu-id="8c301-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-319">'Razor'</span></span>
- <span data-ttu-id="8c301-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-322">'Blazor'</span></span>
- <span data-ttu-id="8c301-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-323">'Identity'</span></span>
- <span data-ttu-id="8c301-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-325">'Razor'</span></span>
- <span data-ttu-id="8c301-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-328">'Blazor'</span></span>
- <span data-ttu-id="8c301-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-329">'Identity'</span></span>
- <span data-ttu-id="8c301-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-331">'Razor'</span></span>
- <span data-ttu-id="8c301-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-334">'Blazor'</span></span>
- <span data-ttu-id="8c301-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-335">'Identity'</span></span>
- <span data-ttu-id="8c301-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-337">'Razor'</span></span>
- <span data-ttu-id="8c301-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-340">'Blazor'</span></span>
- <span data-ttu-id="8c301-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-341">'Identity'</span></span>
- <span data-ttu-id="8c301-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-343">'Razor'</span></span>
- <span data-ttu-id="8c301-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-346">'Blazor'</span></span>
- <span data-ttu-id="8c301-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-347">'Identity'</span></span>
- <span data-ttu-id="8c301-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-349">'Razor'</span></span>
- <span data-ttu-id="8c301-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-352">'Blazor'</span></span>
- <span data-ttu-id="8c301-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-353">'Identity'</span></span>
- <span data-ttu-id="8c301-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-355">'Razor'</span></span>
- <span data-ttu-id="8c301-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-358">'Blazor'</span></span>
- <span data-ttu-id="8c301-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-359">'Identity'</span></span>
- <span data-ttu-id="8c301-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-361">'Razor'</span></span>
- <span data-ttu-id="8c301-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-362">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-363">------------------ | | [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure アプリ構成プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [コマンドライン構成プロバイダー](#clcp) | コマンドライン パラメーター | | [カスタム構成プロバイダー](#custom-configuration-provider) | カスタム ソース | | [環境変数構成プロバイダー](#evcp) | 環境変数 | | [ファイル構成プロバイダー](#file-configuration-provider) | INI、JSON、XML ファイル | | [ファイルごとのキーの構成プロバイダー](#key-per-file-configuration-provider) | ディレクトリ ファイル | | [メモリ構成プロバイダー](#memory-configuration-provider) | メモリ内コレクション | | [シークレット マネージャー](xref:security/app-secrets)  | ユーザー プロファイル ディレクトリのファイル |</span><span class="sxs-lookup"><span data-stu-id="8c301-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="8c301-364">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8c301-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8c301-365">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8c301-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8c301-366">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8c301-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8c301-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8c301-367">*appsettings.json*</span></span>
1. <span data-ttu-id="8c301-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="8c301-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8c301-369">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="8c301-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8c301-370">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8c301-371">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8c301-372">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="8c301-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8c301-373">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8c301-374">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8c301-374">Connection string prefixes</span></span>

<span data-ttu-id="8c301-375">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="8c301-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8c301-376">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c301-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8c301-377">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8c301-378">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8c301-378">Connection string prefix</span></span> | <span data-ttu-id="8c301-379">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-379">Provider</span></span> |
| ---
<span data-ttu-id="8c301-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-381">'Blazor'</span></span>
- <span data-ttu-id="8c301-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-382">'Identity'</span></span>
- <span data-ttu-id="8c301-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-384">'Razor'</span></span>
- <span data-ttu-id="8c301-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-387">'Blazor'</span></span>
- <span data-ttu-id="8c301-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-388">'Identity'</span></span>
- <span data-ttu-id="8c301-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-390">'Razor'</span></span>
- <span data-ttu-id="8c301-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-393">'Blazor'</span></span>
- <span data-ttu-id="8c301-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-394">'Identity'</span></span>
- <span data-ttu-id="8c301-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-396">'Razor'</span></span>
- <span data-ttu-id="8c301-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-399">'Blazor'</span></span>
- <span data-ttu-id="8c301-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-400">'Identity'</span></span>
- <span data-ttu-id="8c301-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-402">'Razor'</span></span>
- <span data-ttu-id="8c301-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-405">'Blazor'</span></span>
- <span data-ttu-id="8c301-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-406">'Identity'</span></span>
- <span data-ttu-id="8c301-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-408">'Razor'</span></span>
- <span data-ttu-id="8c301-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-411">'Blazor'</span></span>
- <span data-ttu-id="8c301-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-412">'Identity'</span></span>
- <span data-ttu-id="8c301-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-414">'Razor'</span></span>
- <span data-ttu-id="8c301-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-417">'Blazor'</span></span>
- <span data-ttu-id="8c301-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-418">'Identity'</span></span>
- <span data-ttu-id="8c301-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-420">'Razor'</span></span>
- <span data-ttu-id="8c301-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-423">'Blazor'</span></span>
- <span data-ttu-id="8c301-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-424">'Identity'</span></span>
- <span data-ttu-id="8c301-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-426">'Razor'</span></span>
- <span data-ttu-id="8c301-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-429">'Blazor'</span></span>
- <span data-ttu-id="8c301-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-430">'Identity'</span></span>
- <span data-ttu-id="8c301-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-432">'Razor'</span></span>
- <span data-ttu-id="8c301-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-435">'Blazor'</span></span>
- <span data-ttu-id="8c301-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-436">'Identity'</span></span>
- <span data-ttu-id="8c301-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-438">'Razor'</span></span>
- <span data-ttu-id="8c301-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-439">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-441">'Blazor'</span></span>
- <span data-ttu-id="8c301-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-442">'Identity'</span></span>
- <span data-ttu-id="8c301-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-444">'Razor'</span></span>
- <span data-ttu-id="8c301-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-447">'Blazor'</span></span>
- <span data-ttu-id="8c301-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-448">'Identity'</span></span>
- <span data-ttu-id="8c301-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-450">'Razor'</span></span>
- <span data-ttu-id="8c301-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-451">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-452">---- | | `CUSTOMCONNSTR_` | カスタム プロバイダー | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="8c301-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="8c301-453">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="8c301-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8c301-454">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8c301-455">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="8c301-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8c301-456">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="8c301-456">Environment variable key</span></span> | <span data-ttu-id="8c301-457">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="8c301-457">Converted configuration key</span></span> | <span data-ttu-id="8c301-458">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="8c301-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="8c301-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-460">'Blazor'</span></span>
- <span data-ttu-id="8c301-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-461">'Identity'</span></span>
- <span data-ttu-id="8c301-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-463">'Razor'</span></span>
- <span data-ttu-id="8c301-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-466">'Blazor'</span></span>
- <span data-ttu-id="8c301-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-467">'Identity'</span></span>
- <span data-ttu-id="8c301-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-469">'Razor'</span></span>
- <span data-ttu-id="8c301-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-472">'Blazor'</span></span>
- <span data-ttu-id="8c301-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-473">'Identity'</span></span>
- <span data-ttu-id="8c301-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-475">'Razor'</span></span>
- <span data-ttu-id="8c301-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-478">'Blazor'</span></span>
- <span data-ttu-id="8c301-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-479">'Identity'</span></span>
- <span data-ttu-id="8c301-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-481">'Razor'</span></span>
- <span data-ttu-id="8c301-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-484">'Blazor'</span></span>
- <span data-ttu-id="8c301-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-485">'Identity'</span></span>
- <span data-ttu-id="8c301-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-487">'Razor'</span></span>
- <span data-ttu-id="8c301-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-490">'Blazor'</span></span>
- <span data-ttu-id="8c301-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-491">'Identity'</span></span>
- <span data-ttu-id="8c301-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-493">'Razor'</span></span>
- <span data-ttu-id="8c301-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-496">'Blazor'</span></span>
- <span data-ttu-id="8c301-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-497">'Identity'</span></span>
- <span data-ttu-id="8c301-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-499">'Razor'</span></span>
- <span data-ttu-id="8c301-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-502">'Blazor'</span></span>
- <span data-ttu-id="8c301-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-503">'Identity'</span></span>
- <span data-ttu-id="8c301-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-505">'Razor'</span></span>
- <span data-ttu-id="8c301-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-508">'Blazor'</span></span>
- <span data-ttu-id="8c301-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-509">'Identity'</span></span>
- <span data-ttu-id="8c301-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-511">'Razor'</span></span>
- <span data-ttu-id="8c301-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-514">'Blazor'</span></span>
- <span data-ttu-id="8c301-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-515">'Identity'</span></span>
- <span data-ttu-id="8c301-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-517">'Razor'</span></span>
- <span data-ttu-id="8c301-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-518">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-520">'Blazor'</span></span>
- <span data-ttu-id="8c301-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-521">'Identity'</span></span>
- <span data-ttu-id="8c301-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-523">'Razor'</span></span>
- <span data-ttu-id="8c301-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-526">'Blazor'</span></span>
- <span data-ttu-id="8c301-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-527">'Identity'</span></span>
- <span data-ttu-id="8c301-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-529">'Razor'</span></span>
- <span data-ttu-id="8c301-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-532">'Blazor'</span></span>
- <span data-ttu-id="8c301-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-533">'Identity'</span></span>
- <span data-ttu-id="8c301-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-535">'Razor'</span></span>
- <span data-ttu-id="8c301-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-538">'Blazor'</span></span>
- <span data-ttu-id="8c301-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-539">'Identity'</span></span>
- <span data-ttu-id="8c301-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-541">'Razor'</span></span>
- <span data-ttu-id="8c301-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-544">'Blazor'</span></span>
- <span data-ttu-id="8c301-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-545">'Identity'</span></span>
- <span data-ttu-id="8c301-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-547">'Razor'</span></span>
- <span data-ttu-id="8c301-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-550">'Blazor'</span></span>
- <span data-ttu-id="8c301-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-551">'Identity'</span></span>
- <span data-ttu-id="8c301-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-553">'Razor'</span></span>
- <span data-ttu-id="8c301-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-556">'Blazor'</span></span>
- <span data-ttu-id="8c301-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-557">'Identity'</span></span>
- <span data-ttu-id="8c301-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-559">'Razor'</span></span>
- <span data-ttu-id="8c301-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-562">'Blazor'</span></span>
- <span data-ttu-id="8c301-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-563">'Identity'</span></span>
- <span data-ttu-id="8c301-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-565">'Razor'</span></span>
- <span data-ttu-id="8c301-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-568">'Blazor'</span></span>
- <span data-ttu-id="8c301-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-569">'Identity'</span></span>
- <span data-ttu-id="8c301-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-571">'Razor'</span></span>
- <span data-ttu-id="8c301-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-574">'Blazor'</span></span>
- <span data-ttu-id="8c301-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-575">'Identity'</span></span>
- <span data-ttu-id="8c301-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-577">'Razor'</span></span>
- <span data-ttu-id="8c301-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-580">'Blazor'</span></span>
- <span data-ttu-id="8c301-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-581">'Identity'</span></span>
- <span data-ttu-id="8c301-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-583">'Razor'</span></span>
- <span data-ttu-id="8c301-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-584">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-586">'Blazor'</span></span>
- <span data-ttu-id="8c301-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-587">'Identity'</span></span>
- <span data-ttu-id="8c301-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-589">'Razor'</span></span>
- <span data-ttu-id="8c301-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-592">'Blazor'</span></span>
- <span data-ttu-id="8c301-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-593">'Identity'</span></span>
- <span data-ttu-id="8c301-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-595">'Razor'</span></span>
- <span data-ttu-id="8c301-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-598">'Blazor'</span></span>
- <span data-ttu-id="8c301-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-599">'Identity'</span></span>
- <span data-ttu-id="8c301-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-601">'Razor'</span></span>
- <span data-ttu-id="8c301-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-604">'Blazor'</span></span>
- <span data-ttu-id="8c301-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-605">'Identity'</span></span>
- <span data-ttu-id="8c301-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-607">'Razor'</span></span>
- <span data-ttu-id="8c301-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-610">'Blazor'</span></span>
- <span data-ttu-id="8c301-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-611">'Identity'</span></span>
- <span data-ttu-id="8c301-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-613">'Razor'</span></span>
- <span data-ttu-id="8c301-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-616">'Blazor'</span></span>
- <span data-ttu-id="8c301-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-617">'Identity'</span></span>
- <span data-ttu-id="8c301-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-619">'Razor'</span></span>
- <span data-ttu-id="8c301-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-622">'Blazor'</span></span>
- <span data-ttu-id="8c301-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-623">'Identity'</span></span>
- <span data-ttu-id="8c301-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-625">'Razor'</span></span>
- <span data-ttu-id="8c301-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-628">'Blazor'</span></span>
- <span data-ttu-id="8c301-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-629">'Identity'</span></span>
- <span data-ttu-id="8c301-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-631">'Razor'</span></span>
- <span data-ttu-id="8c301-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-634">'Blazor'</span></span>
- <span data-ttu-id="8c301-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-635">'Identity'</span></span>
- <span data-ttu-id="8c301-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-637">'Razor'</span></span>
- <span data-ttu-id="8c301-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-640">'Blazor'</span></span>
- <span data-ttu-id="8c301-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-641">'Identity'</span></span>
- <span data-ttu-id="8c301-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-643">'Razor'</span></span>
- <span data-ttu-id="8c301-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-646">'Blazor'</span></span>
- <span data-ttu-id="8c301-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-647">'Identity'</span></span>
- <span data-ttu-id="8c301-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-649">'Razor'</span></span>
- <span data-ttu-id="8c301-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-652">'Blazor'</span></span>
- <span data-ttu-id="8c301-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-653">'Identity'</span></span>
- <span data-ttu-id="8c301-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-655">'Razor'</span></span>
- <span data-ttu-id="8c301-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-658">'Blazor'</span></span>
- <span data-ttu-id="8c301-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-659">'Identity'</span></span>
- <span data-ttu-id="8c301-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-661">'Razor'</span></span>
- <span data-ttu-id="8c301-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-664">'Blazor'</span></span>
- <span data-ttu-id="8c301-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-665">'Identity'</span></span>
- <span data-ttu-id="8c301-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-667">'Razor'</span></span>
- <span data-ttu-id="8c301-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-670">'Blazor'</span></span>
- <span data-ttu-id="8c301-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-671">'Identity'</span></span>
- <span data-ttu-id="8c301-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-673">'Razor'</span></span>
- <span data-ttu-id="8c301-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-676">'Blazor'</span></span>
- <span data-ttu-id="8c301-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-677">'Identity'</span></span>
- <span data-ttu-id="8c301-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-679">'Razor'</span></span>
- <span data-ttu-id="8c301-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-682">'Blazor'</span></span>
- <span data-ttu-id="8c301-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-683">'Identity'</span></span>
- <span data-ttu-id="8c301-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-685">'Razor'</span></span>
- <span data-ttu-id="8c301-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-688">'Blazor'</span></span>
- <span data-ttu-id="8c301-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-689">'Identity'</span></span>
- <span data-ttu-id="8c301-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-691">'Razor'</span></span>
- <span data-ttu-id="8c301-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-694">'Blazor'</span></span>
- <span data-ttu-id="8c301-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-695">'Identity'</span></span>
- <span data-ttu-id="8c301-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-697">'Razor'</span></span>
- <span data-ttu-id="8c301-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-700">'Blazor'</span></span>
- <span data-ttu-id="8c301-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-701">'Identity'</span></span>
- <span data-ttu-id="8c301-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-703">'Razor'</span></span>
- <span data-ttu-id="8c301-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-706">'Blazor'</span></span>
- <span data-ttu-id="8c301-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-707">'Identity'</span></span>
- <span data-ttu-id="8c301-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-709">'Razor'</span></span>
- <span data-ttu-id="8c301-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-712">'Blazor'</span></span>
- <span data-ttu-id="8c301-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-713">'Identity'</span></span>
- <span data-ttu-id="8c301-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-715">'Razor'</span></span>
- <span data-ttu-id="8c301-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-718">'Blazor'</span></span>
- <span data-ttu-id="8c301-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-719">'Identity'</span></span>
- <span data-ttu-id="8c301-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-721">'Razor'</span></span>
- <span data-ttu-id="8c301-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-724">'Blazor'</span></span>
- <span data-ttu-id="8c301-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-725">'Identity'</span></span>
- <span data-ttu-id="8c301-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-727">'Razor'</span></span>
- <span data-ttu-id="8c301-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-730">'Blazor'</span></span>
- <span data-ttu-id="8c301-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-731">'Identity'</span></span>
- <span data-ttu-id="8c301-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-733">'Razor'</span></span>
- <span data-ttu-id="8c301-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-736">'Blazor'</span></span>
- <span data-ttu-id="8c301-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-737">'Identity'</span></span>
- <span data-ttu-id="8c301-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-739">'Razor'</span></span>
- <span data-ttu-id="8c301-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-742">'Blazor'</span></span>
- <span data-ttu-id="8c301-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-743">'Identity'</span></span>
- <span data-ttu-id="8c301-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-745">'Razor'</span></span>
- <span data-ttu-id="8c301-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-748">'Blazor'</span></span>
- <span data-ttu-id="8c301-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-749">'Identity'</span></span>
- <span data-ttu-id="8c301-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-751">'Razor'</span></span>
- <span data-ttu-id="8c301-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-754">'Blazor'</span></span>
- <span data-ttu-id="8c301-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-755">'Identity'</span></span>
- <span data-ttu-id="8c301-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-757">'Razor'</span></span>
- <span data-ttu-id="8c301-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-760">'Blazor'</span></span>
- <span data-ttu-id="8c301-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-761">'Identity'</span></span>
- <span data-ttu-id="8c301-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-763">'Razor'</span></span>
- <span data-ttu-id="8c301-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-766">'Blazor'</span></span>
- <span data-ttu-id="8c301-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-767">'Identity'</span></span>
- <span data-ttu-id="8c301-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-769">'Razor'</span></span>
- <span data-ttu-id="8c301-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-772">'Blazor'</span></span>
- <span data-ttu-id="8c301-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-773">'Identity'</span></span>
- <span data-ttu-id="8c301-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-775">'Razor'</span></span>
- <span data-ttu-id="8c301-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-778">'Blazor'</span></span>
- <span data-ttu-id="8c301-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-779">'Identity'</span></span>
- <span data-ttu-id="8c301-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-781">'Razor'</span></span>
- <span data-ttu-id="8c301-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-784">'Blazor'</span></span>
- <span data-ttu-id="8c301-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-785">'Identity'</span></span>
- <span data-ttu-id="8c301-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-787">'Razor'</span></span>
- <span data-ttu-id="8c301-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-790">'Blazor'</span></span>
- <span data-ttu-id="8c301-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-791">'Identity'</span></span>
- <span data-ttu-id="8c301-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-793">'Razor'</span></span>
- <span data-ttu-id="8c301-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-796">'Blazor'</span></span>
- <span data-ttu-id="8c301-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-797">'Identity'</span></span>
- <span data-ttu-id="8c301-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-799">'Razor'</span></span>
- <span data-ttu-id="8c301-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-802">'Blazor'</span></span>
- <span data-ttu-id="8c301-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-803">'Identity'</span></span>
- <span data-ttu-id="8c301-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-805">'Razor'</span></span>
- <span data-ttu-id="8c301-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-806">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="8c301-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-809">値: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-810">値: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-811">値: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="8c301-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8c301-812">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-812">JSON configuration provider</span></span>

<span data-ttu-id="8c301-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8c301-814">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="8c301-814">Overloads can specify:</span></span>

* <span data-ttu-id="8c301-815">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-815">Whether the file is optional.</span></span>
* <span data-ttu-id="8c301-816">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8c301-817">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="8c301-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8c301-818">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8c301-818">The preceding code:</span></span>

* <span data-ttu-id="8c301-819">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="8c301-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8c301-820">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="8c301-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8c301-821">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8c301-822">*MyConfig.json* ファイルの前に[既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8c301-823">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="8c301-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8c301-824">通常は、[環境変数構成プロバイダー](#evcp)および[コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="8c301-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8c301-825">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8c301-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8c301-826">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="8c301-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8c301-827">*appsettings.json* と *appsettings*.`Environment`.*json* ファイルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8c301-828">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8c301-829">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8c301-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8c301-830">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8c301-831">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-831">File configuration provider</span></span>

<span data-ttu-id="8c301-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="8c301-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8c301-833">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="8c301-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8c301-834">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8c301-835">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8c301-836">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8c301-837">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-837">INI configuration provider</span></span>

<span data-ttu-id="8c301-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-839">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8c301-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8c301-840">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="8c301-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8c301-841">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8c301-842">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8c301-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8c301-843">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8c301-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8c301-844">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8c301-845">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-845">XML configuration provider</span></span>

<span data-ttu-id="8c301-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-847">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8c301-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8c301-848">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="8c301-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8c301-849">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8c301-850">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8c301-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8c301-851">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8c301-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8c301-852">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-853">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="8c301-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8c301-854">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8c301-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-855">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8c301-856">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8c301-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8c301-857">key:attribute</span></span>
* <span data-ttu-id="8c301-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8c301-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8c301-859">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="8c301-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8c301-861">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8c301-861">The key is the file name.</span></span> <span data-ttu-id="8c301-862">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-862">The value contains the file's contents.</span></span> <span data-ttu-id="8c301-863">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8c301-864">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8c301-865">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8c301-866">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="8c301-867">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="8c301-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8c301-868">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="8c301-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8c301-869">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8c301-870">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8c301-871">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8c301-872">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-872">Memory configuration provider</span></span>

<span data-ttu-id="8c301-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8c301-874">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="8c301-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8c301-875">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8c301-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-876">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8c301-877">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8c301-878">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8c301-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="8c301-879">GetValue</span></span>

<span data-ttu-id="8c301-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="8c301-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-881">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8c301-882">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="8c301-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8c301-883">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="8c301-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8c301-884">以下のコードでは、*MySubsection セクション*を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="8c301-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8c301-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="8c301-885">GetSection</span></span>

<span data-ttu-id="8c301-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="8c301-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8c301-887">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-888">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-889">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8c301-890">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8c301-891">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8c301-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8c301-893">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="8c301-893">GetChildren and Exists</span></span>

<span data-ttu-id="8c301-894">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-895">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="8c301-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8c301-896">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="8c301-896">Bind an array</span></span>

<span data-ttu-id="8c301-897">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c301-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8c301-898">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8c301-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8c301-899">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="8c301-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8c301-900">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="8c301-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8c301-901">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8c301-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-902">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8c301-903">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="8c301-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8c301-904">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="8c301-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8c301-905">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="8c301-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8c301-906">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="8c301-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8c301-907">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8c301-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-908">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8c301-909">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8c301-910">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8c301-911">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8c301-912">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="8c301-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8c301-913">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="8c301-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8c301-914">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="8c301-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8c301-915">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8c301-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-916">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8c301-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8c301-917">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8c301-918">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-918">Custom configuration provider</span></span>

<span data-ttu-id="8c301-919">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8c301-920">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8c301-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8c301-921">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8c301-922">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8c301-923">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8c301-924">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="8c301-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8c301-925">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8c301-926">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="8c301-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8c301-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8c301-928">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8c301-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8c301-930"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8c301-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8c301-932"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8c301-933">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8c301-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8c301-934">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8c301-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8c301-936">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8c301-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8c301-938">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8c301-939">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="8c301-939">Access configuration in Startup</span></span>

<span data-ttu-id="8c301-940">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8c301-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8c301-941">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="8c301-942">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8c301-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8c301-943">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8c301-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8c301-944">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="8c301-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8c301-945">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8c301-946">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="8c301-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8c301-947">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8c301-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8c301-948">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="8c301-948">Configure options with a delegate</span></span>

<span data-ttu-id="8c301-949">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8c301-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8c301-950">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="8c301-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8c301-951">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8c301-952">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8c301-953">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8c301-954">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8c301-955">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8c301-955">Host versus app configuration</span></span>

<span data-ttu-id="8c301-956">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8c301-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8c301-957">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8c301-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8c301-958">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8c301-959">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8c301-960">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8c301-961">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="8c301-961">Default host configuration</span></span>

<span data-ttu-id="8c301-962">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8c301-963">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8c301-964">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8c301-965">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8c301-966">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8c301-967">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="8c301-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8c301-968">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8c301-969">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8c301-970">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8c301-971">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8c301-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8c301-972">その他の構成</span><span class="sxs-lookup"><span data-stu-id="8c301-972">Other configuration</span></span>

<span data-ttu-id="8c301-973">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="8c301-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8c301-974">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8c301-975">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8c301-976"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="8c301-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8c301-977">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="8c301-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8c301-978">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8c301-979">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8c301-980">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="8c301-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="8c301-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8c301-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8c301-982">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c301-983">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8c301-983">Additional resources</span></span>

* [<span data-ttu-id="8c301-984">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="8c301-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c301-985">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="8c301-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8c301-986">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8c301-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8c301-987">Azure Key Vault</span></span>
* <span data-ttu-id="8c301-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8c301-988">Azure App Configuration</span></span>
* <span data-ttu-id="8c301-989">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8c301-989">Command-line arguments</span></span>
* <span data-ttu-id="8c301-990">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="8c301-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8c301-991">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8c301-991">Directory files</span></span>
* <span data-ttu-id="8c301-992">環境変数</span><span class="sxs-lookup"><span data-stu-id="8c301-992">Environment variables</span></span>
* <span data-ttu-id="8c301-993">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="8c301-993">In-memory .NET objects</span></span>
* <span data-ttu-id="8c301-994">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="8c301-994">Settings files</span></span>

<span data-ttu-id="8c301-995">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8c301-996">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="8c301-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8c301-997">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="8c301-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8c301-998">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="8c301-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8c301-999">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8c301-1000">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8c301-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8c301-1001">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8c301-1001">Host versus app configuration</span></span>

<span data-ttu-id="8c301-1002">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8c301-1003">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8c301-1004">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8c301-1005">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8c301-1006">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8c301-1007">その他の構成</span><span class="sxs-lookup"><span data-stu-id="8c301-1007">Other configuration</span></span>

<span data-ttu-id="8c301-1008">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8c301-1009">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8c301-1010">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8c301-1011"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="8c301-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8c301-1012">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="8c301-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8c301-1013">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8c301-1014">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8c301-1015">既定の構成</span><span class="sxs-lookup"><span data-stu-id="8c301-1015">Default configuration</span></span>

<span data-ttu-id="8c301-1016">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8c301-1017">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8c301-1018">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8c301-1019">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8c301-1020">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8c301-1021">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8c301-1022">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8c301-1023">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8c301-1024">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="8c301-1025">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="8c301-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8c301-1026">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8c301-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8c301-1027">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8c301-1028">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8c301-1029">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8c301-1030">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="8c301-1030">Security</span></span>

<span data-ttu-id="8c301-1031">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8c301-1032">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8c301-1033">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8c301-1034">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8c301-1035">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8c301-1036"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8c301-1037">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8c301-1038">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8c301-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8c301-1040">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8c301-1041">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="8c301-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="8c301-1042">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8c301-1043">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8c301-1044">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8c301-1045">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8c301-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-1046">section0:key0</span></span>
* <span data-ttu-id="8c301-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-1047">section0:key1</span></span>
* <span data-ttu-id="8c301-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-1048">section1:key0</span></span>
* <span data-ttu-id="8c301-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-1049">section1:key1</span></span>

<span data-ttu-id="8c301-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8c301-1051">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8c301-1052">規約</span><span class="sxs-lookup"><span data-stu-id="8c301-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8c301-1053">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1053">Sources and providers</span></span>

<span data-ttu-id="8c301-1054">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8c301-1055">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8c301-1056">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8c301-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8c301-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8c301-1059">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8c301-1060">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="8c301-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8c301-1061">キー</span><span class="sxs-lookup"><span data-stu-id="8c301-1061">Keys</span></span>

<span data-ttu-id="8c301-1062">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8c301-1063">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="8c301-1064">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8c301-1065">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8c301-1066">階層キー</span><span class="sxs-lookup"><span data-stu-id="8c301-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="8c301-1067">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8c301-1068">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8c301-1069">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8c301-1070">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8c301-1071">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8c301-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8c301-1073">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8c301-1074">値</span><span class="sxs-lookup"><span data-stu-id="8c301-1074">Values</span></span>

<span data-ttu-id="8c301-1075">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8c301-1076">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="8c301-1076">Values are strings.</span></span>
* <span data-ttu-id="8c301-1077">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8c301-1078">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1078">Providers</span></span>

<span data-ttu-id="8c301-1079">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8c301-1080">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1080">Provider</span></span> | <span data-ttu-id="8c301-1081">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="8c301-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1083">'Blazor'</span></span>
- <span data-ttu-id="8c301-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1084">'Identity'</span></span>
- <span data-ttu-id="8c301-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1086">'Razor'</span></span>
- <span data-ttu-id="8c301-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1089">'Blazor'</span></span>
- <span data-ttu-id="8c301-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1090">'Identity'</span></span>
- <span data-ttu-id="8c301-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1092">'Razor'</span></span>
- <span data-ttu-id="8c301-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1095">'Blazor'</span></span>
- <span data-ttu-id="8c301-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1096">'Identity'</span></span>
- <span data-ttu-id="8c301-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1098">'Razor'</span></span>
- <span data-ttu-id="8c301-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1101">'Blazor'</span></span>
- <span data-ttu-id="8c301-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1102">'Identity'</span></span>
- <span data-ttu-id="8c301-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1104">'Razor'</span></span>
- <span data-ttu-id="8c301-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1107">'Blazor'</span></span>
- <span data-ttu-id="8c301-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1108">'Identity'</span></span>
- <span data-ttu-id="8c301-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1110">'Razor'</span></span>
- <span data-ttu-id="8c301-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1113">'Blazor'</span></span>
- <span data-ttu-id="8c301-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1114">'Identity'</span></span>
- <span data-ttu-id="8c301-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1116">'Razor'</span></span>
- <span data-ttu-id="8c301-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1119">'Blazor'</span></span>
- <span data-ttu-id="8c301-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1120">'Identity'</span></span>
- <span data-ttu-id="8c301-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1122">'Razor'</span></span>
- <span data-ttu-id="8c301-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1125">'Blazor'</span></span>
- <span data-ttu-id="8c301-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1126">'Identity'</span></span>
- <span data-ttu-id="8c301-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1128">'Razor'</span></span>
- <span data-ttu-id="8c301-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1131">'Blazor'</span></span>
- <span data-ttu-id="8c301-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1132">'Identity'</span></span>
- <span data-ttu-id="8c301-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1134">'Razor'</span></span>
- <span data-ttu-id="8c301-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1137">'Blazor'</span></span>
- <span data-ttu-id="8c301-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1138">'Identity'</span></span>
- <span data-ttu-id="8c301-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1140">'Razor'</span></span>
- <span data-ttu-id="8c301-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1143">'Blazor'</span></span>
- <span data-ttu-id="8c301-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1144">'Identity'</span></span>
- <span data-ttu-id="8c301-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1146">'Razor'</span></span>
- <span data-ttu-id="8c301-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1149">'Blazor'</span></span>
- <span data-ttu-id="8c301-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1150">'Identity'</span></span>
- <span data-ttu-id="8c301-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1152">'Razor'</span></span>
- <span data-ttu-id="8c301-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1155">'Blazor'</span></span>
- <span data-ttu-id="8c301-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1156">'Identity'</span></span>
- <span data-ttu-id="8c301-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1158">'Razor'</span></span>
- <span data-ttu-id="8c301-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1161">'Blazor'</span></span>
- <span data-ttu-id="8c301-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1162">'Identity'</span></span>
- <span data-ttu-id="8c301-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1164">'Razor'</span></span>
- <span data-ttu-id="8c301-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1167">'Blazor'</span></span>
- <span data-ttu-id="8c301-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1168">'Identity'</span></span>
- <span data-ttu-id="8c301-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1170">'Razor'</span></span>
- <span data-ttu-id="8c301-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1173">'Blazor'</span></span>
- <span data-ttu-id="8c301-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1174">'Identity'</span></span>
- <span data-ttu-id="8c301-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1176">'Razor'</span></span>
- <span data-ttu-id="8c301-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1179">'Blazor'</span></span>
- <span data-ttu-id="8c301-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1180">'Identity'</span></span>
- <span data-ttu-id="8c301-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1182">'Razor'</span></span>
- <span data-ttu-id="8c301-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1184">------------------ | | [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) (*セキュリティ*のトピック) | Azure Key Vault | | [Azure アプリ構成プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure ドキュメント) | Azure App Configuration | | [コマンドライン構成プロバイダー](#command-line-configuration-provider) | コマンドライン パラメーター | | [カスタム構成プロバイダー](#custom-configuration-provider) | カスタム ソース | | [環境変数構成プロバイダー](#environment-variables-configuration-provider) | 環境変数 | | [ファイル構成プロバイダー](#file-configuration-provider) | ファイル (INI、JSON、XML) | | [ファイルごとのキーの構成プロバイダー](#key-per-file-configuration-provider) | ディレクトリ ファイル | | [メモリ構成プロバイダー](#memory-configuration-provider) | メモリ内コレクション | | [ユーザー シークレット (シークレット マネージャー)](xref:security/app-secrets) (*セキュリティ*のトピック) | ユーザー プロファイル ディレクトリのファイル |</span><span class="sxs-lookup"><span data-stu-id="8c301-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="8c301-1185">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8c301-1186">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8c301-1187">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8c301-1188">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8c301-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8c301-1189">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="8c301-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8c301-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8c301-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8c301-1191">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="8c301-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8c301-1192">環境変数</span><span class="sxs-lookup"><span data-stu-id="8c301-1192">Environment variables</span></span>
1. <span data-ttu-id="8c301-1193">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8c301-1193">Command-line arguments</span></span>

<span data-ttu-id="8c301-1194">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="8c301-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8c301-1195">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c301-1196">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8c301-1197">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="8c301-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8c301-1198">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8c301-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8c301-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8c301-1200">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8c301-1201">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="8c301-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8c301-1202">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8c301-1203">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="8c301-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8c301-1204">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8c301-1205">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="8c301-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="8c301-1206">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8c301-1207">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8c301-1208">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="8c301-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-1210">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8c301-1211">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8c301-1212">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8c301-1213">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8c301-1214">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8c301-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8c301-1215">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8c301-1216">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1216">Environment variables.</span></span>

<span data-ttu-id="8c301-1217">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8c301-1218">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8c301-1219">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8c301-1220">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8c301-1221">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="8c301-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c301-1222">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8c301-1223">**例**</span><span class="sxs-lookup"><span data-stu-id="8c301-1223">**Example**</span></span>

<span data-ttu-id="8c301-1224">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8c301-1225">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8c301-1226">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8c301-1227">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8c301-1228">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8c301-1229">引数</span><span class="sxs-lookup"><span data-stu-id="8c301-1229">Arguments</span></span>

<span data-ttu-id="8c301-1230">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8c301-1231">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8c301-1232">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8c301-1232">Key prefix</span></span>               | <span data-ttu-id="8c301-1233">例</span><span class="sxs-lookup"><span data-stu-id="8c301-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="8c301-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1235">'Blazor'</span></span>
- <span data-ttu-id="8c301-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1236">'Identity'</span></span>
- <span data-ttu-id="8c301-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1238">'Razor'</span></span>
- <span data-ttu-id="8c301-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1241">'Blazor'</span></span>
- <span data-ttu-id="8c301-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1242">'Identity'</span></span>
- <span data-ttu-id="8c301-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1244">'Razor'</span></span>
- <span data-ttu-id="8c301-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1247">'Blazor'</span></span>
- <span data-ttu-id="8c301-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1248">'Identity'</span></span>
- <span data-ttu-id="8c301-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1250">'Razor'</span></span>
- <span data-ttu-id="8c301-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1253">'Blazor'</span></span>
- <span data-ttu-id="8c301-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1254">'Identity'</span></span>
- <span data-ttu-id="8c301-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1256">'Razor'</span></span>
- <span data-ttu-id="8c301-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1259">'Blazor'</span></span>
- <span data-ttu-id="8c301-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1260">'Identity'</span></span>
- <span data-ttu-id="8c301-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1262">'Razor'</span></span>
- <span data-ttu-id="8c301-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1265">'Blazor'</span></span>
- <span data-ttu-id="8c301-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1266">'Identity'</span></span>
- <span data-ttu-id="8c301-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1268">'Razor'</span></span>
- <span data-ttu-id="8c301-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1271">'Blazor'</span></span>
- <span data-ttu-id="8c301-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1272">'Identity'</span></span>
- <span data-ttu-id="8c301-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1274">'Razor'</span></span>
- <span data-ttu-id="8c301-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1277">'Blazor'</span></span>
- <span data-ttu-id="8c301-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1278">'Identity'</span></span>
- <span data-ttu-id="8c301-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1280">'Razor'</span></span>
- <span data-ttu-id="8c301-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1283">'Blazor'</span></span>
- <span data-ttu-id="8c301-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1284">'Identity'</span></span>
- <span data-ttu-id="8c301-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1286">'Razor'</span></span>
- <span data-ttu-id="8c301-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1289">'Blazor'</span></span>
- <span data-ttu-id="8c301-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1290">'Identity'</span></span>
- <span data-ttu-id="8c301-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1292">'Razor'</span></span>
- <span data-ttu-id="8c301-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1295">'Blazor'</span></span>
- <span data-ttu-id="8c301-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1296">'Identity'</span></span>
- <span data-ttu-id="8c301-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1298">'Razor'</span></span>
- <span data-ttu-id="8c301-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1301">'Blazor'</span></span>
- <span data-ttu-id="8c301-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1302">'Identity'</span></span>
- <span data-ttu-id="8c301-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1304">'Razor'</span></span>
- <span data-ttu-id="8c301-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1307">'Blazor'</span></span>
- <span data-ttu-id="8c301-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1308">'Identity'</span></span>
- <span data-ttu-id="8c301-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1310">'Razor'</span></span>
- <span data-ttu-id="8c301-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1313">'Blazor'</span></span>
- <span data-ttu-id="8c301-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1314">'Identity'</span></span>
- <span data-ttu-id="8c301-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1316">'Razor'</span></span>
- <span data-ttu-id="8c301-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1319">'Blazor'</span></span>
- <span data-ttu-id="8c301-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1320">'Identity'</span></span>
- <span data-ttu-id="8c301-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1322">'Razor'</span></span>
- <span data-ttu-id="8c301-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1325">'Blazor'</span></span>
- <span data-ttu-id="8c301-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1326">'Identity'</span></span>
- <span data-ttu-id="8c301-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1328">'Razor'</span></span>
- <span data-ttu-id="8c301-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1331">'Blazor'</span></span>
- <span data-ttu-id="8c301-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1332">'Identity'</span></span>
- <span data-ttu-id="8c301-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1334">'Razor'</span></span>
- <span data-ttu-id="8c301-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1337">'Blazor'</span></span>
- <span data-ttu-id="8c301-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1338">'Identity'</span></span>
- <span data-ttu-id="8c301-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1340">'Razor'</span></span>
- <span data-ttu-id="8c301-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1343">'Blazor'</span></span>
- <span data-ttu-id="8c301-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1344">'Identity'</span></span>
- <span data-ttu-id="8c301-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1346">'Razor'</span></span>
- <span data-ttu-id="8c301-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1349">'Blazor'</span></span>
- <span data-ttu-id="8c301-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1350">'Identity'</span></span>
- <span data-ttu-id="8c301-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1352">'Razor'</span></span>
- <span data-ttu-id="8c301-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1355">'Blazor'</span></span>
- <span data-ttu-id="8c301-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1356">'Identity'</span></span>
- <span data-ttu-id="8c301-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1358">'Razor'</span></span>
- <span data-ttu-id="8c301-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1361">'Blazor'</span></span>
- <span data-ttu-id="8c301-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1362">'Identity'</span></span>
- <span data-ttu-id="8c301-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1364">'Razor'</span></span>
- <span data-ttu-id="8c301-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1367">'Blazor'</span></span>
- <span data-ttu-id="8c301-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1368">'Identity'</span></span>
- <span data-ttu-id="8c301-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1370">'Razor'</span></span>
- <span data-ttu-id="8c301-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1373">'Blazor'</span></span>
- <span data-ttu-id="8c301-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1374">'Identity'</span></span>
- <span data-ttu-id="8c301-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1376">'Razor'</span></span>
- <span data-ttu-id="8c301-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1379">'Blazor'</span></span>
- <span data-ttu-id="8c301-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1380">'Identity'</span></span>
- <span data-ttu-id="8c301-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1382">'Razor'</span></span>
- <span data-ttu-id="8c301-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1385">'Blazor'</span></span>
- <span data-ttu-id="8c301-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1386">'Identity'</span></span>
- <span data-ttu-id="8c301-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1388">'Razor'</span></span>
- <span data-ttu-id="8c301-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1391">'Blazor'</span></span>
- <span data-ttu-id="8c301-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1392">'Identity'</span></span>
- <span data-ttu-id="8c301-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1394">'Razor'</span></span>
- <span data-ttu-id="8c301-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1397">'Blazor'</span></span>
- <span data-ttu-id="8c301-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1398">'Identity'</span></span>
- <span data-ttu-id="8c301-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1400">'Razor'</span></span>
- <span data-ttu-id="8c301-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1403">'Blazor'</span></span>
- <span data-ttu-id="8c301-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1404">'Identity'</span></span>
- <span data-ttu-id="8c301-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1406">'Razor'</span></span>
- <span data-ttu-id="8c301-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1409">'Blazor'</span></span>
- <span data-ttu-id="8c301-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1410">'Identity'</span></span>
- <span data-ttu-id="8c301-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1412">'Razor'</span></span>
- <span data-ttu-id="8c301-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1415">'Blazor'</span></span>
- <span data-ttu-id="8c301-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1416">'Identity'</span></span>
- <span data-ttu-id="8c301-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1418">'Razor'</span></span>
- <span data-ttu-id="8c301-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1421">'Blazor'</span></span>
- <span data-ttu-id="8c301-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1422">'Identity'</span></span>
- <span data-ttu-id="8c301-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1424">'Razor'</span></span>
- <span data-ttu-id="8c301-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1427">'Blazor'</span></span>
- <span data-ttu-id="8c301-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1428">'Identity'</span></span>
- <span data-ttu-id="8c301-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1430">'Razor'</span></span>
- <span data-ttu-id="8c301-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1433">'Blazor'</span></span>
- <span data-ttu-id="8c301-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1434">'Identity'</span></span>
- <span data-ttu-id="8c301-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1436">'Razor'</span></span>
- <span data-ttu-id="8c301-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1439">'Blazor'</span></span>
- <span data-ttu-id="8c301-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1440">'Identity'</span></span>
- <span data-ttu-id="8c301-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1442">'Razor'</span></span>
- <span data-ttu-id="8c301-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1444">--------------------------- | | プレフィックスなし                | `CommandLineKey1=value1`                               |
| 2 つのダッシュ (`--`)        | `--CommandLineKey2=value2`、`--CommandLineKey2 value2` |
| フォワード スラッシュ (`/`)      | `/CommandLineKey3=value3`、`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="8c301-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="8c301-1445">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8c301-1446">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="8c301-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8c301-1447">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="8c301-1447">Switch mappings</span></span>

<span data-ttu-id="8c301-1448">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8c301-1449"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8c301-1450">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8c301-1451">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8c301-1452">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="8c301-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8c301-1453">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="8c301-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8c301-1454">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8c301-1455">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8c301-1456">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8c301-1457">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8c301-1458">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8c301-1459">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8c301-1460">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c301-1461">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8c301-1462">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8c301-1463">Key</span><span class="sxs-lookup"><span data-stu-id="8c301-1463">Key</span></span>       | <span data-ttu-id="8c301-1464">[値]</span><span class="sxs-lookup"><span data-stu-id="8c301-1464">Value</span></span>             |
| ---
<span data-ttu-id="8c301-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1466">'Blazor'</span></span>
- <span data-ttu-id="8c301-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1467">'Identity'</span></span>
- <span data-ttu-id="8c301-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1469">'Razor'</span></span>
- <span data-ttu-id="8c301-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1472">'Blazor'</span></span>
- <span data-ttu-id="8c301-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1473">'Identity'</span></span>
- <span data-ttu-id="8c301-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1475">'Razor'</span></span>
- <span data-ttu-id="8c301-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1478">'Blazor'</span></span>
- <span data-ttu-id="8c301-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1479">'Identity'</span></span>
- <span data-ttu-id="8c301-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1481">'Razor'</span></span>
- <span data-ttu-id="8c301-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1484">'Blazor'</span></span>
- <span data-ttu-id="8c301-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1485">'Identity'</span></span>
- <span data-ttu-id="8c301-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1487">'Razor'</span></span>
- <span data-ttu-id="8c301-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1490">'Blazor'</span></span>
- <span data-ttu-id="8c301-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1491">'Identity'</span></span>
- <span data-ttu-id="8c301-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1493">'Razor'</span></span>
- <span data-ttu-id="8c301-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1496">'Blazor'</span></span>
- <span data-ttu-id="8c301-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1497">'Identity'</span></span>
- <span data-ttu-id="8c301-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1499">'Razor'</span></span>
- <span data-ttu-id="8c301-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1502">'Blazor'</span></span>
- <span data-ttu-id="8c301-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1503">'Identity'</span></span>
- <span data-ttu-id="8c301-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1505">'Razor'</span></span>
- <span data-ttu-id="8c301-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1508">'Blazor'</span></span>
- <span data-ttu-id="8c301-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1509">'Identity'</span></span>
- <span data-ttu-id="8c301-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1511">'Razor'</span></span>
- <span data-ttu-id="8c301-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="8c301-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="8c301-1514">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8c301-1515">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8c301-1516">Key</span><span class="sxs-lookup"><span data-stu-id="8c301-1516">Key</span></span>               | <span data-ttu-id="8c301-1517">[値]</span><span class="sxs-lookup"><span data-stu-id="8c301-1517">Value</span></span>    |
| ---
<span data-ttu-id="8c301-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1519">'Blazor'</span></span>
- <span data-ttu-id="8c301-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1520">'Identity'</span></span>
- <span data-ttu-id="8c301-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1522">'Razor'</span></span>
- <span data-ttu-id="8c301-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1525">'Blazor'</span></span>
- <span data-ttu-id="8c301-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1526">'Identity'</span></span>
- <span data-ttu-id="8c301-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1528">'Razor'</span></span>
- <span data-ttu-id="8c301-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1531">'Blazor'</span></span>
- <span data-ttu-id="8c301-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1532">'Identity'</span></span>
- <span data-ttu-id="8c301-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1534">'Razor'</span></span>
- <span data-ttu-id="8c301-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1537">'Blazor'</span></span>
- <span data-ttu-id="8c301-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1538">'Identity'</span></span>
- <span data-ttu-id="8c301-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1540">'Razor'</span></span>
- <span data-ttu-id="8c301-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1543">'Blazor'</span></span>
- <span data-ttu-id="8c301-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1544">'Identity'</span></span>
- <span data-ttu-id="8c301-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1546">'Razor'</span></span>
- <span data-ttu-id="8c301-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1549">'Blazor'</span></span>
- <span data-ttu-id="8c301-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1550">'Identity'</span></span>
- <span data-ttu-id="8c301-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1552">'Razor'</span></span>
- <span data-ttu-id="8c301-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1555">'Blazor'</span></span>
- <span data-ttu-id="8c301-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1556">'Identity'</span></span>
- <span data-ttu-id="8c301-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1558">'Razor'</span></span>
- <span data-ttu-id="8c301-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1561">'Blazor'</span></span>
- <span data-ttu-id="8c301-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1562">'Identity'</span></span>
- <span data-ttu-id="8c301-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1564">'Razor'</span></span>
- <span data-ttu-id="8c301-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="8c301-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8c301-1567">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8c301-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-1569">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8c301-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8c301-1571">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8c301-1572">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8c301-1573">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8c301-1574">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8c301-1575">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="8c301-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8c301-1576">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8c301-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8c301-1577">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8c301-1578">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-1578">Command-line arguments.</span></span>

<span data-ttu-id="8c301-1579">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8c301-1580">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8c301-1581">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8c301-1582">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="8c301-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8c301-1583">**例**</span><span class="sxs-lookup"><span data-stu-id="8c301-1583">**Example**</span></span>

<span data-ttu-id="8c301-1584">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8c301-1585">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1585">Run the sample app.</span></span> <span data-ttu-id="8c301-1586">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8c301-1587">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8c301-1588">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8c301-1589">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8c301-1590">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8c301-1591">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8c301-1592">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="8c301-1592">Prefixes</span></span>

<span data-ttu-id="8c301-1593">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8c301-1594">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8c301-1595">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8c301-1596">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8c301-1597">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8c301-1598">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="8c301-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="8c301-1599">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8c301-1600">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8c301-1601">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8c301-1601">Connection string prefix</span></span> | <span data-ttu-id="8c301-1602">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-1602">Provider</span></span> |
| ---
<span data-ttu-id="8c301-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1604">'Blazor'</span></span>
- <span data-ttu-id="8c301-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1605">'Identity'</span></span>
- <span data-ttu-id="8c301-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1607">'Razor'</span></span>
- <span data-ttu-id="8c301-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1610">'Blazor'</span></span>
- <span data-ttu-id="8c301-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1611">'Identity'</span></span>
- <span data-ttu-id="8c301-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1613">'Razor'</span></span>
- <span data-ttu-id="8c301-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1616">'Blazor'</span></span>
- <span data-ttu-id="8c301-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1617">'Identity'</span></span>
- <span data-ttu-id="8c301-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1619">'Razor'</span></span>
- <span data-ttu-id="8c301-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1622">'Blazor'</span></span>
- <span data-ttu-id="8c301-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1623">'Identity'</span></span>
- <span data-ttu-id="8c301-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1625">'Razor'</span></span>
- <span data-ttu-id="8c301-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1628">'Blazor'</span></span>
- <span data-ttu-id="8c301-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1629">'Identity'</span></span>
- <span data-ttu-id="8c301-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1631">'Razor'</span></span>
- <span data-ttu-id="8c301-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1634">'Blazor'</span></span>
- <span data-ttu-id="8c301-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1635">'Identity'</span></span>
- <span data-ttu-id="8c301-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1637">'Razor'</span></span>
- <span data-ttu-id="8c301-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1640">'Blazor'</span></span>
- <span data-ttu-id="8c301-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1641">'Identity'</span></span>
- <span data-ttu-id="8c301-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1643">'Razor'</span></span>
- <span data-ttu-id="8c301-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1646">'Blazor'</span></span>
- <span data-ttu-id="8c301-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1647">'Identity'</span></span>
- <span data-ttu-id="8c301-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1649">'Razor'</span></span>
- <span data-ttu-id="8c301-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1652">'Blazor'</span></span>
- <span data-ttu-id="8c301-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1653">'Identity'</span></span>
- <span data-ttu-id="8c301-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1655">'Razor'</span></span>
- <span data-ttu-id="8c301-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1658">'Blazor'</span></span>
- <span data-ttu-id="8c301-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1659">'Identity'</span></span>
- <span data-ttu-id="8c301-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1661">'Razor'</span></span>
- <span data-ttu-id="8c301-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1664">'Blazor'</span></span>
- <span data-ttu-id="8c301-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1665">'Identity'</span></span>
- <span data-ttu-id="8c301-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1667">'Razor'</span></span>
- <span data-ttu-id="8c301-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1670">'Blazor'</span></span>
- <span data-ttu-id="8c301-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1671">'Identity'</span></span>
- <span data-ttu-id="8c301-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1673">'Razor'</span></span>
- <span data-ttu-id="8c301-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1675">---- | | `CUSTOMCONNSTR_` | カスタム プロバイダー | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="8c301-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="8c301-1676">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="8c301-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8c301-1677">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8c301-1678">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="8c301-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8c301-1679">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="8c301-1679">Environment variable key</span></span> | <span data-ttu-id="8c301-1680">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="8c301-1680">Converted configuration key</span></span> | <span data-ttu-id="8c301-1681">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="8c301-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="8c301-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1683">'Blazor'</span></span>
- <span data-ttu-id="8c301-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1684">'Identity'</span></span>
- <span data-ttu-id="8c301-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1686">'Razor'</span></span>
- <span data-ttu-id="8c301-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1689">'Blazor'</span></span>
- <span data-ttu-id="8c301-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1690">'Identity'</span></span>
- <span data-ttu-id="8c301-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1692">'Razor'</span></span>
- <span data-ttu-id="8c301-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1695">'Blazor'</span></span>
- <span data-ttu-id="8c301-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1696">'Identity'</span></span>
- <span data-ttu-id="8c301-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1698">'Razor'</span></span>
- <span data-ttu-id="8c301-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1701">'Blazor'</span></span>
- <span data-ttu-id="8c301-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1702">'Identity'</span></span>
- <span data-ttu-id="8c301-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1704">'Razor'</span></span>
- <span data-ttu-id="8c301-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1707">'Blazor'</span></span>
- <span data-ttu-id="8c301-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1708">'Identity'</span></span>
- <span data-ttu-id="8c301-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1710">'Razor'</span></span>
- <span data-ttu-id="8c301-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1713">'Blazor'</span></span>
- <span data-ttu-id="8c301-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1714">'Identity'</span></span>
- <span data-ttu-id="8c301-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1716">'Razor'</span></span>
- <span data-ttu-id="8c301-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1719">'Blazor'</span></span>
- <span data-ttu-id="8c301-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1720">'Identity'</span></span>
- <span data-ttu-id="8c301-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1722">'Razor'</span></span>
- <span data-ttu-id="8c301-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1725">'Blazor'</span></span>
- <span data-ttu-id="8c301-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1726">'Identity'</span></span>
- <span data-ttu-id="8c301-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1728">'Razor'</span></span>
- <span data-ttu-id="8c301-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1731">'Blazor'</span></span>
- <span data-ttu-id="8c301-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1732">'Identity'</span></span>
- <span data-ttu-id="8c301-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1734">'Razor'</span></span>
- <span data-ttu-id="8c301-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1737">'Blazor'</span></span>
- <span data-ttu-id="8c301-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1738">'Identity'</span></span>
- <span data-ttu-id="8c301-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1740">'Razor'</span></span>
- <span data-ttu-id="8c301-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1743">'Blazor'</span></span>
- <span data-ttu-id="8c301-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1744">'Identity'</span></span>
- <span data-ttu-id="8c301-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1746">'Razor'</span></span>
- <span data-ttu-id="8c301-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1749">'Blazor'</span></span>
- <span data-ttu-id="8c301-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1750">'Identity'</span></span>
- <span data-ttu-id="8c301-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1752">'Razor'</span></span>
- <span data-ttu-id="8c301-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1755">'Blazor'</span></span>
- <span data-ttu-id="8c301-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1756">'Identity'</span></span>
- <span data-ttu-id="8c301-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1758">'Razor'</span></span>
- <span data-ttu-id="8c301-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1761">'Blazor'</span></span>
- <span data-ttu-id="8c301-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1762">'Identity'</span></span>
- <span data-ttu-id="8c301-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1764">'Razor'</span></span>
- <span data-ttu-id="8c301-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1767">'Blazor'</span></span>
- <span data-ttu-id="8c301-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1768">'Identity'</span></span>
- <span data-ttu-id="8c301-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1770">'Razor'</span></span>
- <span data-ttu-id="8c301-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1773">'Blazor'</span></span>
- <span data-ttu-id="8c301-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1774">'Identity'</span></span>
- <span data-ttu-id="8c301-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1776">'Razor'</span></span>
- <span data-ttu-id="8c301-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1779">'Blazor'</span></span>
- <span data-ttu-id="8c301-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1780">'Identity'</span></span>
- <span data-ttu-id="8c301-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1782">'Razor'</span></span>
- <span data-ttu-id="8c301-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1785">'Blazor'</span></span>
- <span data-ttu-id="8c301-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1786">'Identity'</span></span>
- <span data-ttu-id="8c301-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1788">'Razor'</span></span>
- <span data-ttu-id="8c301-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1791">'Blazor'</span></span>
- <span data-ttu-id="8c301-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1792">'Identity'</span></span>
- <span data-ttu-id="8c301-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1794">'Razor'</span></span>
- <span data-ttu-id="8c301-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1797">'Blazor'</span></span>
- <span data-ttu-id="8c301-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1798">'Identity'</span></span>
- <span data-ttu-id="8c301-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1800">'Razor'</span></span>
- <span data-ttu-id="8c301-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1803">'Blazor'</span></span>
- <span data-ttu-id="8c301-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1804">'Identity'</span></span>
- <span data-ttu-id="8c301-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1806">'Razor'</span></span>
- <span data-ttu-id="8c301-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1809">'Blazor'</span></span>
- <span data-ttu-id="8c301-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1810">'Identity'</span></span>
- <span data-ttu-id="8c301-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1812">'Razor'</span></span>
- <span data-ttu-id="8c301-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1815">'Blazor'</span></span>
- <span data-ttu-id="8c301-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1816">'Identity'</span></span>
- <span data-ttu-id="8c301-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1818">'Razor'</span></span>
- <span data-ttu-id="8c301-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1821">'Blazor'</span></span>
- <span data-ttu-id="8c301-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1822">'Identity'</span></span>
- <span data-ttu-id="8c301-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1824">'Razor'</span></span>
- <span data-ttu-id="8c301-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1827">'Blazor'</span></span>
- <span data-ttu-id="8c301-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1828">'Identity'</span></span>
- <span data-ttu-id="8c301-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1830">'Razor'</span></span>
- <span data-ttu-id="8c301-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1833">'Blazor'</span></span>
- <span data-ttu-id="8c301-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1834">'Identity'</span></span>
- <span data-ttu-id="8c301-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1836">'Razor'</span></span>
- <span data-ttu-id="8c301-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1839">'Blazor'</span></span>
- <span data-ttu-id="8c301-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1840">'Identity'</span></span>
- <span data-ttu-id="8c301-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1842">'Razor'</span></span>
- <span data-ttu-id="8c301-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1845">'Blazor'</span></span>
- <span data-ttu-id="8c301-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1846">'Identity'</span></span>
- <span data-ttu-id="8c301-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1848">'Razor'</span></span>
- <span data-ttu-id="8c301-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1851">'Blazor'</span></span>
- <span data-ttu-id="8c301-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1852">'Identity'</span></span>
- <span data-ttu-id="8c301-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1854">'Razor'</span></span>
- <span data-ttu-id="8c301-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1857">'Blazor'</span></span>
- <span data-ttu-id="8c301-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1858">'Identity'</span></span>
- <span data-ttu-id="8c301-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1860">'Razor'</span></span>
- <span data-ttu-id="8c301-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1863">'Blazor'</span></span>
- <span data-ttu-id="8c301-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1864">'Identity'</span></span>
- <span data-ttu-id="8c301-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1866">'Razor'</span></span>
- <span data-ttu-id="8c301-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1869">'Blazor'</span></span>
- <span data-ttu-id="8c301-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1870">'Identity'</span></span>
- <span data-ttu-id="8c301-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1872">'Razor'</span></span>
- <span data-ttu-id="8c301-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1875">'Blazor'</span></span>
- <span data-ttu-id="8c301-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1876">'Identity'</span></span>
- <span data-ttu-id="8c301-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1878">'Razor'</span></span>
- <span data-ttu-id="8c301-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1881">'Blazor'</span></span>
- <span data-ttu-id="8c301-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1882">'Identity'</span></span>
- <span data-ttu-id="8c301-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1884">'Razor'</span></span>
- <span data-ttu-id="8c301-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1887">'Blazor'</span></span>
- <span data-ttu-id="8c301-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1888">'Identity'</span></span>
- <span data-ttu-id="8c301-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1890">'Razor'</span></span>
- <span data-ttu-id="8c301-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1893">'Blazor'</span></span>
- <span data-ttu-id="8c301-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1894">'Identity'</span></span>
- <span data-ttu-id="8c301-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1896">'Razor'</span></span>
- <span data-ttu-id="8c301-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1899">'Blazor'</span></span>
- <span data-ttu-id="8c301-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1900">'Identity'</span></span>
- <span data-ttu-id="8c301-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1902">'Razor'</span></span>
- <span data-ttu-id="8c301-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1905">'Blazor'</span></span>
- <span data-ttu-id="8c301-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1906">'Identity'</span></span>
- <span data-ttu-id="8c301-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1908">'Razor'</span></span>
- <span data-ttu-id="8c301-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1911">'Blazor'</span></span>
- <span data-ttu-id="8c301-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1912">'Identity'</span></span>
- <span data-ttu-id="8c301-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1914">'Razor'</span></span>
- <span data-ttu-id="8c301-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1917">'Blazor'</span></span>
- <span data-ttu-id="8c301-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1918">'Identity'</span></span>
- <span data-ttu-id="8c301-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1920">'Razor'</span></span>
- <span data-ttu-id="8c301-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1923">'Blazor'</span></span>
- <span data-ttu-id="8c301-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1924">'Identity'</span></span>
- <span data-ttu-id="8c301-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1926">'Razor'</span></span>
- <span data-ttu-id="8c301-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1929">'Blazor'</span></span>
- <span data-ttu-id="8c301-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1930">'Identity'</span></span>
- <span data-ttu-id="8c301-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1932">'Razor'</span></span>
- <span data-ttu-id="8c301-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1935">'Blazor'</span></span>
- <span data-ttu-id="8c301-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1936">'Identity'</span></span>
- <span data-ttu-id="8c301-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1938">'Razor'</span></span>
- <span data-ttu-id="8c301-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1941">'Blazor'</span></span>
- <span data-ttu-id="8c301-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1942">'Identity'</span></span>
- <span data-ttu-id="8c301-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1944">'Razor'</span></span>
- <span data-ttu-id="8c301-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1947">'Blazor'</span></span>
- <span data-ttu-id="8c301-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1948">'Identity'</span></span>
- <span data-ttu-id="8c301-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1950">'Razor'</span></span>
- <span data-ttu-id="8c301-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1953">'Blazor'</span></span>
- <span data-ttu-id="8c301-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1954">'Identity'</span></span>
- <span data-ttu-id="8c301-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1956">'Razor'</span></span>
- <span data-ttu-id="8c301-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1959">'Blazor'</span></span>
- <span data-ttu-id="8c301-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1960">'Identity'</span></span>
- <span data-ttu-id="8c301-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1962">'Razor'</span></span>
- <span data-ttu-id="8c301-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1965">'Blazor'</span></span>
- <span data-ttu-id="8c301-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1966">'Identity'</span></span>
- <span data-ttu-id="8c301-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1968">'Razor'</span></span>
- <span data-ttu-id="8c301-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1971">'Blazor'</span></span>
- <span data-ttu-id="8c301-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1972">'Identity'</span></span>
- <span data-ttu-id="8c301-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1974">'Razor'</span></span>
- <span data-ttu-id="8c301-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1977">'Blazor'</span></span>
- <span data-ttu-id="8c301-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1978">'Identity'</span></span>
- <span data-ttu-id="8c301-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1980">'Razor'</span></span>
- <span data-ttu-id="8c301-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1983">'Blazor'</span></span>
- <span data-ttu-id="8c301-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1984">'Identity'</span></span>
- <span data-ttu-id="8c301-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1986">'Razor'</span></span>
- <span data-ttu-id="8c301-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1989">'Blazor'</span></span>
- <span data-ttu-id="8c301-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1990">'Identity'</span></span>
- <span data-ttu-id="8c301-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1992">'Razor'</span></span>
- <span data-ttu-id="8c301-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1995">'Blazor'</span></span>
- <span data-ttu-id="8c301-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-1996">'Identity'</span></span>
- <span data-ttu-id="8c301-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-1998">'Razor'</span></span>
- <span data-ttu-id="8c301-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2001">'Blazor'</span></span>
- <span data-ttu-id="8c301-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2002">'Identity'</span></span>
- <span data-ttu-id="8c301-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2004">'Razor'</span></span>
- <span data-ttu-id="8c301-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2007">'Blazor'</span></span>
- <span data-ttu-id="8c301-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2008">'Identity'</span></span>
- <span data-ttu-id="8c301-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2010">'Razor'</span></span>
- <span data-ttu-id="8c301-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2013">'Blazor'</span></span>
- <span data-ttu-id="8c301-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2014">'Identity'</span></span>
- <span data-ttu-id="8c301-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2016">'Razor'</span></span>
- <span data-ttu-id="8c301-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2019">'Blazor'</span></span>
- <span data-ttu-id="8c301-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2020">'Identity'</span></span>
- <span data-ttu-id="8c301-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2022">'Razor'</span></span>
- <span data-ttu-id="8c301-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2025">'Blazor'</span></span>
- <span data-ttu-id="8c301-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2026">'Identity'</span></span>
- <span data-ttu-id="8c301-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2028">'Razor'</span></span>
- <span data-ttu-id="8c301-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="8c301-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-2032">値: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-2033">値: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8c301-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8c301-2034">値: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="8c301-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="8c301-2035">**例**</span><span class="sxs-lookup"><span data-stu-id="8c301-2035">**Example**</span></span>

<span data-ttu-id="8c301-2036">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8c301-2037">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8c301-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8c301-2038">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8c301-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8c301-2039">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8c301-2040">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2040">File Configuration Provider</span></span>

<span data-ttu-id="8c301-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="8c301-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8c301-2042">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8c301-2043">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8c301-2044">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8c301-2045">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8c301-2046">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2046">INI Configuration Provider</span></span>

<span data-ttu-id="8c301-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-2048">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8c301-2049">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8c301-2050">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="8c301-2051">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="8c301-2052">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8c301-2053">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8c301-2054">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8c301-2055">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="8c301-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8c301-2056">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8c301-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2057">section0:key0</span></span>
* <span data-ttu-id="8c301-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2058">section0:key1</span></span>
* <span data-ttu-id="8c301-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8c301-2059">section1:subsection:key</span></span>
* <span data-ttu-id="8c301-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8c301-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="8c301-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8c301-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8c301-2062">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="8c301-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8c301-2064">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8c301-2065">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="8c301-2066">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="8c301-2067">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8c301-2068">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8c301-2069">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c301-2070">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8c301-2071">*appsettings.json*:このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="8c301-2072">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8c301-2073">*appsettings.{Environment}.json*:ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8c301-2074">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8c301-2075">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8c301-2076">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8c301-2076">Environment variables.</span></span>
* <span data-ttu-id="8c301-2077">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8c301-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8c301-2078">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8c301-2078">Command-line arguments.</span></span>

<span data-ttu-id="8c301-2079">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8c301-2080">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8c301-2081">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8c301-2082">**例**</span><span class="sxs-lookup"><span data-stu-id="8c301-2082">**Example**</span></span>

<span data-ttu-id="8c301-2083">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8c301-2084">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8c301-2085">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8c301-2086">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8c301-2087">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2087">Run the sample app.</span></span> <span data-ttu-id="8c301-2088">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8c301-2089">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c301-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8c301-2090">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8c301-2091">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8c301-2092">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8c301-2093">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8c301-2094">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8c301-2095">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8c301-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8c301-2096">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8c301-2097">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2097">XML Configuration Provider</span></span>

<span data-ttu-id="8c301-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8c301-2099">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8c301-2100">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="8c301-2101">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="8c301-2102">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8c301-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8c301-2103">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8c301-2104">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8c301-2105">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="8c301-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8c301-2106">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8c301-2107">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8c301-2108">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8c301-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2109">section0:key0</span></span>
* <span data-ttu-id="8c301-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2110">section0:key1</span></span>
* <span data-ttu-id="8c301-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2111">section1:key0</span></span>
* <span data-ttu-id="8c301-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2112">section1:key1</span></span>

<span data-ttu-id="8c301-2113">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8c301-2114">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8c301-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="8c301-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="8c301-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="8c301-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2118">section:section1:key:key1</span></span>

<span data-ttu-id="8c301-2119">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8c301-2120">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8c301-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8c301-2121">key:attribute</span></span>
* <span data-ttu-id="8c301-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8c301-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8c301-2123">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8c301-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8c301-2125">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2125">The key is the file name.</span></span> <span data-ttu-id="8c301-2126">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2126">The value contains the file's contents.</span></span> <span data-ttu-id="8c301-2127">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8c301-2128">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8c301-2129">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8c301-2130">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="8c301-2131">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="8c301-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8c301-2132">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="8c301-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8c301-2133">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8c301-2134">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8c301-2135">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8c301-2136">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="8c301-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8c301-2138">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8c301-2139">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8c301-2140">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8c301-2141">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8c301-2142">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8c301-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="8c301-2143">GetValue</span></span>

<span data-ttu-id="8c301-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8c301-2145">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="8c301-2146">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2146">The following example:</span></span>

* <span data-ttu-id="8c301-2147">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8c301-2148">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8c301-2149">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="8c301-2150">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8c301-2151">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="8c301-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8c301-2152">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8c301-2153">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8c301-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8c301-2154">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8c301-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2155">section0:key0</span></span>
* <span data-ttu-id="8c301-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2156">section0:key1</span></span>
* <span data-ttu-id="8c301-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2157">section1:key0</span></span>
* <span data-ttu-id="8c301-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2158">section1:key1</span></span>
* <span data-ttu-id="8c301-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="8c301-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="8c301-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8c301-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="8c301-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8c301-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8c301-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="8c301-2163">GetSection</span></span>

<span data-ttu-id="8c301-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8c301-2165">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8c301-2166">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8c301-2167">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8c301-2168">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8c301-2169">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8c301-2170">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8c301-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8c301-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8c301-2172">GetChildren</span></span>

<span data-ttu-id="8c301-2173">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8c301-2174">既存</span><span class="sxs-lookup"><span data-stu-id="8c301-2174">Exists</span></span>

<span data-ttu-id="8c301-2175">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8c301-2176">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8c301-2177">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="8c301-2177">Bind to an object graph</span></span>

<span data-ttu-id="8c301-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8c301-2179">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8c301-2180">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8c301-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8c301-2181">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8c301-2182">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8c301-2183">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8c301-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8c301-2185">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8c301-2186">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="8c301-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8c301-2187">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8c301-2187">Bind an array to a class</span></span>

<span data-ttu-id="8c301-2188">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="8c301-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8c301-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c301-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8c301-2190">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8c301-2191">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2191">Binding is provided by convention.</span></span> <span data-ttu-id="8c301-2192">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8c301-2193">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8c301-2193">**In-memory array processing**</span></span>

<span data-ttu-id="8c301-2194">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8c301-2195">Key</span><span class="sxs-lookup"><span data-stu-id="8c301-2195">Key</span></span>             | <span data-ttu-id="8c301-2196">[値]</span><span class="sxs-lookup"><span data-stu-id="8c301-2196">Value</span></span>  |
| :---
<span data-ttu-id="8c301-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2198">'Blazor'</span></span>
- <span data-ttu-id="8c301-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2199">'Identity'</span></span>
- <span data-ttu-id="8c301-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2201">'Razor'</span></span>
- <span data-ttu-id="8c301-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2204">'Blazor'</span></span>
- <span data-ttu-id="8c301-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2205">'Identity'</span></span>
- <span data-ttu-id="8c301-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2207">'Razor'</span></span>
- <span data-ttu-id="8c301-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2210">'Blazor'</span></span>
- <span data-ttu-id="8c301-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2211">'Identity'</span></span>
- <span data-ttu-id="8c301-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2213">'Razor'</span></span>
- <span data-ttu-id="8c301-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2216">'Blazor'</span></span>
- <span data-ttu-id="8c301-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2217">'Identity'</span></span>
- <span data-ttu-id="8c301-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2219">'Razor'</span></span>
- <span data-ttu-id="8c301-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="8c301-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="8c301-2222">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8c301-2223">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="8c301-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8c301-2224">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8c301-2225">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8c301-2226">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8c301-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="8c301-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8c301-2228">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8c301-2229">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8c301-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8c301-2230">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8c301-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="8c301-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2232">'Blazor'</span></span>
- <span data-ttu-id="8c301-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2233">'Identity'</span></span>
- <span data-ttu-id="8c301-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2235">'Razor'</span></span>
- <span data-ttu-id="8c301-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2238">'Blazor'</span></span>
- <span data-ttu-id="8c301-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2239">'Identity'</span></span>
- <span data-ttu-id="8c301-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2241">'Razor'</span></span>
- <span data-ttu-id="8c301-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2244">'Blazor'</span></span>
- <span data-ttu-id="8c301-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2245">'Identity'</span></span>
- <span data-ttu-id="8c301-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2247">'Razor'</span></span>
- <span data-ttu-id="8c301-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2250">'Blazor'</span></span>
- <span data-ttu-id="8c301-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2251">'Identity'</span></span>
- <span data-ttu-id="8c301-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2253">'Razor'</span></span>
- <span data-ttu-id="8c301-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2256">'Blazor'</span></span>
- <span data-ttu-id="8c301-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2257">'Identity'</span></span>
- <span data-ttu-id="8c301-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2259">'Razor'</span></span>
- <span data-ttu-id="8c301-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2262">'Blazor'</span></span>
- <span data-ttu-id="8c301-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2263">'Identity'</span></span>
- <span data-ttu-id="8c301-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2265">'Razor'</span></span>
- <span data-ttu-id="8c301-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2268">'Blazor'</span></span>
- <span data-ttu-id="8c301-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2269">'Identity'</span></span>
- <span data-ttu-id="8c301-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2271">'Razor'</span></span>
- <span data-ttu-id="8c301-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2274">'Blazor'</span></span>
- <span data-ttu-id="8c301-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2275">'Identity'</span></span>
- <span data-ttu-id="8c301-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2277">'Razor'</span></span>
- <span data-ttu-id="8c301-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2280">'Blazor'</span></span>
- <span data-ttu-id="8c301-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2281">'Identity'</span></span>
- <span data-ttu-id="8c301-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2283">'Razor'</span></span>
- <span data-ttu-id="8c301-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2286">'Blazor'</span></span>
- <span data-ttu-id="8c301-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2287">'Identity'</span></span>
- <span data-ttu-id="8c301-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2289">'Razor'</span></span>
- <span data-ttu-id="8c301-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2292">'Blazor'</span></span>
- <span data-ttu-id="8c301-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2293">'Identity'</span></span>
- <span data-ttu-id="8c301-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2295">'Razor'</span></span>
- <span data-ttu-id="8c301-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2298">'Blazor'</span></span>
- <span data-ttu-id="8c301-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2299">'Identity'</span></span>
- <span data-ttu-id="8c301-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2301">'Razor'</span></span>
- <span data-ttu-id="8c301-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2304">'Blazor'</span></span>
- <span data-ttu-id="8c301-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2305">'Identity'</span></span>
- <span data-ttu-id="8c301-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2307">'Razor'</span></span>
- <span data-ttu-id="8c301-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2310">'Blazor'</span></span>
- <span data-ttu-id="8c301-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2311">'Identity'</span></span>
- <span data-ttu-id="8c301-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2313">'Razor'</span></span>
- <span data-ttu-id="8c301-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2316">'Blazor'</span></span>
- <span data-ttu-id="8c301-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2317">'Identity'</span></span>
- <span data-ttu-id="8c301-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2319">'Razor'</span></span>
- <span data-ttu-id="8c301-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2322">'Blazor'</span></span>
- <span data-ttu-id="8c301-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2323">'Identity'</span></span>
- <span data-ttu-id="8c301-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2325">'Razor'</span></span>
- <span data-ttu-id="8c301-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2328">'Blazor'</span></span>
- <span data-ttu-id="8c301-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2329">'Identity'</span></span>
- <span data-ttu-id="8c301-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2331">'Razor'</span></span>
- <span data-ttu-id="8c301-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2334">'Blazor'</span></span>
- <span data-ttu-id="8c301-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2335">'Identity'</span></span>
- <span data-ttu-id="8c301-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2337">'Razor'</span></span>
- <span data-ttu-id="8c301-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2340">'Blazor'</span></span>
- <span data-ttu-id="8c301-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2341">'Identity'</span></span>
- <span data-ttu-id="8c301-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2343">'Razor'</span></span>
- <span data-ttu-id="8c301-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2346">'Blazor'</span></span>
- <span data-ttu-id="8c301-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2347">'Identity'</span></span>
- <span data-ttu-id="8c301-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2349">'Razor'</span></span>
- <span data-ttu-id="8c301-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2352">'Blazor'</span></span>
- <span data-ttu-id="8c301-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2353">'Identity'</span></span>
- <span data-ttu-id="8c301-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2355">'Razor'</span></span>
- <span data-ttu-id="8c301-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2358">'Blazor'</span></span>
- <span data-ttu-id="8c301-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2359">'Identity'</span></span>
- <span data-ttu-id="8c301-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2361">'Razor'</span></span>
- <span data-ttu-id="8c301-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="8c301-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="8c301-2364">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8c301-2365">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8c301-2366">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8c301-2367">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8c301-2368">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8c301-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8c301-2370">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="8c301-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8c301-2371">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8c301-2372">Key</span><span class="sxs-lookup"><span data-stu-id="8c301-2372">Key</span></span>             | <span data-ttu-id="8c301-2373">[値]</span><span class="sxs-lookup"><span data-stu-id="8c301-2373">Value</span></span>  |
| :---
<span data-ttu-id="8c301-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2375">'Blazor'</span></span>
- <span data-ttu-id="8c301-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2376">'Identity'</span></span>
- <span data-ttu-id="8c301-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2378">'Razor'</span></span>
- <span data-ttu-id="8c301-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2381">'Blazor'</span></span>
- <span data-ttu-id="8c301-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2382">'Identity'</span></span>
- <span data-ttu-id="8c301-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2384">'Razor'</span></span>
- <span data-ttu-id="8c301-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2387">'Blazor'</span></span>
- <span data-ttu-id="8c301-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2388">'Identity'</span></span>
- <span data-ttu-id="8c301-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2390">'Razor'</span></span>
- <span data-ttu-id="8c301-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2393">'Blazor'</span></span>
- <span data-ttu-id="8c301-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2394">'Identity'</span></span>
- <span data-ttu-id="8c301-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2396">'Razor'</span></span>
- <span data-ttu-id="8c301-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="8c301-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="8c301-2399">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8c301-2400">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8c301-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8c301-2401">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8c301-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="8c301-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2403">'Blazor'</span></span>
- <span data-ttu-id="8c301-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2404">'Identity'</span></span>
- <span data-ttu-id="8c301-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2406">'Razor'</span></span>
- <span data-ttu-id="8c301-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2409">'Blazor'</span></span>
- <span data-ttu-id="8c301-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2410">'Identity'</span></span>
- <span data-ttu-id="8c301-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2412">'Razor'</span></span>
- <span data-ttu-id="8c301-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2415">'Blazor'</span></span>
- <span data-ttu-id="8c301-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2416">'Identity'</span></span>
- <span data-ttu-id="8c301-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2418">'Razor'</span></span>
- <span data-ttu-id="8c301-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2421">'Blazor'</span></span>
- <span data-ttu-id="8c301-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2422">'Identity'</span></span>
- <span data-ttu-id="8c301-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2424">'Razor'</span></span>
- <span data-ttu-id="8c301-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2427">'Blazor'</span></span>
- <span data-ttu-id="8c301-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2428">'Identity'</span></span>
- <span data-ttu-id="8c301-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2430">'Razor'</span></span>
- <span data-ttu-id="8c301-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2433">'Blazor'</span></span>
- <span data-ttu-id="8c301-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2434">'Identity'</span></span>
- <span data-ttu-id="8c301-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2436">'Razor'</span></span>
- <span data-ttu-id="8c301-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2439">'Blazor'</span></span>
- <span data-ttu-id="8c301-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2440">'Identity'</span></span>
- <span data-ttu-id="8c301-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2442">'Razor'</span></span>
- <span data-ttu-id="8c301-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2445">'Blazor'</span></span>
- <span data-ttu-id="8c301-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2446">'Identity'</span></span>
- <span data-ttu-id="8c301-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2448">'Razor'</span></span>
- <span data-ttu-id="8c301-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2451">'Blazor'</span></span>
- <span data-ttu-id="8c301-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2452">'Identity'</span></span>
- <span data-ttu-id="8c301-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2454">'Razor'</span></span>
- <span data-ttu-id="8c301-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2457">'Blazor'</span></span>
- <span data-ttu-id="8c301-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2458">'Identity'</span></span>
- <span data-ttu-id="8c301-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2460">'Razor'</span></span>
- <span data-ttu-id="8c301-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2463">'Blazor'</span></span>
- <span data-ttu-id="8c301-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2464">'Identity'</span></span>
- <span data-ttu-id="8c301-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2466">'Razor'</span></span>
- <span data-ttu-id="8c301-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2469">'Blazor'</span></span>
- <span data-ttu-id="8c301-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2470">'Identity'</span></span>
- <span data-ttu-id="8c301-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2472">'Razor'</span></span>
- <span data-ttu-id="8c301-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2475">'Blazor'</span></span>
- <span data-ttu-id="8c301-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2476">'Identity'</span></span>
- <span data-ttu-id="8c301-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2478">'Razor'</span></span>
- <span data-ttu-id="8c301-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2481">'Blazor'</span></span>
- <span data-ttu-id="8c301-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2482">'Identity'</span></span>
- <span data-ttu-id="8c301-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2484">'Razor'</span></span>
- <span data-ttu-id="8c301-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2487">'Blazor'</span></span>
- <span data-ttu-id="8c301-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2488">'Identity'</span></span>
- <span data-ttu-id="8c301-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2490">'Razor'</span></span>
- <span data-ttu-id="8c301-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2493">'Blazor'</span></span>
- <span data-ttu-id="8c301-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2494">'Identity'</span></span>
- <span data-ttu-id="8c301-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2496">'Razor'</span></span>
- <span data-ttu-id="8c301-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2499">'Blazor'</span></span>
- <span data-ttu-id="8c301-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2500">'Identity'</span></span>
- <span data-ttu-id="8c301-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2502">'Razor'</span></span>
- <span data-ttu-id="8c301-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2505">'Blazor'</span></span>
- <span data-ttu-id="8c301-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2506">'Identity'</span></span>
- <span data-ttu-id="8c301-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2508">'Razor'</span></span>
- <span data-ttu-id="8c301-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2511">'Blazor'</span></span>
- <span data-ttu-id="8c301-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2512">'Identity'</span></span>
- <span data-ttu-id="8c301-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2514">'Razor'</span></span>
- <span data-ttu-id="8c301-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2517">'Blazor'</span></span>
- <span data-ttu-id="8c301-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2518">'Identity'</span></span>
- <span data-ttu-id="8c301-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2520">'Razor'</span></span>
- <span data-ttu-id="8c301-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2523">'Blazor'</span></span>
- <span data-ttu-id="8c301-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2524">'Identity'</span></span>
- <span data-ttu-id="8c301-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2526">'Razor'</span></span>
- <span data-ttu-id="8c301-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2529">'Blazor'</span></span>
- <span data-ttu-id="8c301-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2530">'Identity'</span></span>
- <span data-ttu-id="8c301-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2532">'Razor'</span></span>
- <span data-ttu-id="8c301-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="8c301-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="8c301-2535">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8c301-2535">**JSON array processing**</span></span>

<span data-ttu-id="8c301-2536">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8c301-2537">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="8c301-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8c301-2538">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8c301-2539">Key</span><span class="sxs-lookup"><span data-stu-id="8c301-2539">Key</span></span>                     | <span data-ttu-id="8c301-2540">[値]</span><span class="sxs-lookup"><span data-stu-id="8c301-2540">Value</span></span>  |
| ---
<span data-ttu-id="8c301-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2542">'Blazor'</span></span>
- <span data-ttu-id="8c301-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2543">'Identity'</span></span>
- <span data-ttu-id="8c301-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2545">'Razor'</span></span>
- <span data-ttu-id="8c301-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2548">'Blazor'</span></span>
- <span data-ttu-id="8c301-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2549">'Identity'</span></span>
- <span data-ttu-id="8c301-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2551">'Razor'</span></span>
- <span data-ttu-id="8c301-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2554">'Blazor'</span></span>
- <span data-ttu-id="8c301-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2555">'Identity'</span></span>
- <span data-ttu-id="8c301-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2557">'Razor'</span></span>
- <span data-ttu-id="8c301-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2560">'Blazor'</span></span>
- <span data-ttu-id="8c301-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2561">'Identity'</span></span>
- <span data-ttu-id="8c301-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2563">'Razor'</span></span>
- <span data-ttu-id="8c301-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2566">'Blazor'</span></span>
- <span data-ttu-id="8c301-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2567">'Identity'</span></span>
- <span data-ttu-id="8c301-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2569">'Razor'</span></span>
- <span data-ttu-id="8c301-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2572">'Blazor'</span></span>
- <span data-ttu-id="8c301-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2573">'Identity'</span></span>
- <span data-ttu-id="8c301-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2575">'Razor'</span></span>
- <span data-ttu-id="8c301-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2578">'Blazor'</span></span>
- <span data-ttu-id="8c301-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2579">'Identity'</span></span>
- <span data-ttu-id="8c301-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2581">'Razor'</span></span>
- <span data-ttu-id="8c301-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2584">'Blazor'</span></span>
- <span data-ttu-id="8c301-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2585">'Identity'</span></span>
- <span data-ttu-id="8c301-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2587">'Razor'</span></span>
- <span data-ttu-id="8c301-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2590">'Blazor'</span></span>
- <span data-ttu-id="8c301-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2591">'Identity'</span></span>
- <span data-ttu-id="8c301-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2593">'Razor'</span></span>
- <span data-ttu-id="8c301-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="8c301-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="8c301-2596">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8c301-2597">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8c301-2598">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8c301-2599">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="8c301-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8c301-2600">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="8c301-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="8c301-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2602">'Blazor'</span></span>
- <span data-ttu-id="8c301-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2603">'Identity'</span></span>
- <span data-ttu-id="8c301-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2605">'Razor'</span></span>
- <span data-ttu-id="8c301-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2608">'Blazor'</span></span>
- <span data-ttu-id="8c301-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2609">'Identity'</span></span>
- <span data-ttu-id="8c301-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2611">'Razor'</span></span>
- <span data-ttu-id="8c301-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2614">'Blazor'</span></span>
- <span data-ttu-id="8c301-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2615">'Identity'</span></span>
- <span data-ttu-id="8c301-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2617">'Razor'</span></span>
- <span data-ttu-id="8c301-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2620">'Blazor'</span></span>
- <span data-ttu-id="8c301-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2621">'Identity'</span></span>
- <span data-ttu-id="8c301-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2623">'Razor'</span></span>
- <span data-ttu-id="8c301-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2626">'Blazor'</span></span>
- <span data-ttu-id="8c301-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2627">'Identity'</span></span>
- <span data-ttu-id="8c301-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2629">'Razor'</span></span>
- <span data-ttu-id="8c301-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2632">'Blazor'</span></span>
- <span data-ttu-id="8c301-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2633">'Identity'</span></span>
- <span data-ttu-id="8c301-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2635">'Razor'</span></span>
- <span data-ttu-id="8c301-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2638">'Blazor'</span></span>
- <span data-ttu-id="8c301-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2639">'Identity'</span></span>
- <span data-ttu-id="8c301-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2641">'Razor'</span></span>
- <span data-ttu-id="8c301-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2644">'Blazor'</span></span>
- <span data-ttu-id="8c301-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2645">'Identity'</span></span>
- <span data-ttu-id="8c301-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2647">'Razor'</span></span>
- <span data-ttu-id="8c301-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2650">'Blazor'</span></span>
- <span data-ttu-id="8c301-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2651">'Identity'</span></span>
- <span data-ttu-id="8c301-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2653">'Razor'</span></span>
- <span data-ttu-id="8c301-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2656">'Blazor'</span></span>
- <span data-ttu-id="8c301-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2657">'Identity'</span></span>
- <span data-ttu-id="8c301-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2659">'Razor'</span></span>
- <span data-ttu-id="8c301-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2662">'Blazor'</span></span>
- <span data-ttu-id="8c301-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2663">'Identity'</span></span>
- <span data-ttu-id="8c301-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2665">'Razor'</span></span>
- <span data-ttu-id="8c301-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2668">'Blazor'</span></span>
- <span data-ttu-id="8c301-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2669">'Identity'</span></span>
- <span data-ttu-id="8c301-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2671">'Razor'</span></span>
- <span data-ttu-id="8c301-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2674">'Blazor'</span></span>
- <span data-ttu-id="8c301-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2675">'Identity'</span></span>
- <span data-ttu-id="8c301-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2677">'Razor'</span></span>
- <span data-ttu-id="8c301-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2680">'Blazor'</span></span>
- <span data-ttu-id="8c301-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2681">'Identity'</span></span>
- <span data-ttu-id="8c301-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2683">'Razor'</span></span>
- <span data-ttu-id="8c301-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2686">'Blazor'</span></span>
- <span data-ttu-id="8c301-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2687">'Identity'</span></span>
- <span data-ttu-id="8c301-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2689">'Razor'</span></span>
- <span data-ttu-id="8c301-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2692">'Blazor'</span></span>
- <span data-ttu-id="8c301-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2693">'Identity'</span></span>
- <span data-ttu-id="8c301-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2695">'Razor'</span></span>
- <span data-ttu-id="8c301-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2698">'Blazor'</span></span>
- <span data-ttu-id="8c301-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2699">'Identity'</span></span>
- <span data-ttu-id="8c301-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2701">'Razor'</span></span>
- <span data-ttu-id="8c301-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2704">'Blazor'</span></span>
- <span data-ttu-id="8c301-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2705">'Identity'</span></span>
- <span data-ttu-id="8c301-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2707">'Razor'</span></span>
- <span data-ttu-id="8c301-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2710">'Blazor'</span></span>
- <span data-ttu-id="8c301-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2711">'Identity'</span></span>
- <span data-ttu-id="8c301-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2713">'Razor'</span></span>
- <span data-ttu-id="8c301-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2716">'Blazor'</span></span>
- <span data-ttu-id="8c301-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2717">'Identity'</span></span>
- <span data-ttu-id="8c301-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2719">'Razor'</span></span>
- <span data-ttu-id="8c301-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2722">'Blazor'</span></span>
- <span data-ttu-id="8c301-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2723">'Identity'</span></span>
- <span data-ttu-id="8c301-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2725">'Razor'</span></span>
- <span data-ttu-id="8c301-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2728">'Blazor'</span></span>
- <span data-ttu-id="8c301-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2729">'Identity'</span></span>
- <span data-ttu-id="8c301-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2731">'Razor'</span></span>
- <span data-ttu-id="8c301-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2734">'Blazor'</span></span>
- <span data-ttu-id="8c301-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2735">'Identity'</span></span>
- <span data-ttu-id="8c301-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2737">'Razor'</span></span>
- <span data-ttu-id="8c301-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2740">'Blazor'</span></span>
- <span data-ttu-id="8c301-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2741">'Identity'</span></span>
- <span data-ttu-id="8c301-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2743">'Razor'</span></span>
- <span data-ttu-id="8c301-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2746">'Blazor'</span></span>
- <span data-ttu-id="8c301-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2747">'Identity'</span></span>
- <span data-ttu-id="8c301-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2749">'Razor'</span></span>
- <span data-ttu-id="8c301-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2752">'Blazor'</span></span>
- <span data-ttu-id="8c301-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2753">'Identity'</span></span>
- <span data-ttu-id="8c301-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2755">'Razor'</span></span>
- <span data-ttu-id="8c301-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2758">'Blazor'</span></span>
- <span data-ttu-id="8c301-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2759">'Identity'</span></span>
- <span data-ttu-id="8c301-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2761">'Razor'</span></span>
- <span data-ttu-id="8c301-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c301-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c301-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c301-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2764">'Blazor'</span></span>
- <span data-ttu-id="8c301-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8c301-2765">'Identity'</span></span>
- <span data-ttu-id="8c301-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8c301-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c301-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8c301-2767">'Razor'</span></span>
- <span data-ttu-id="8c301-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c301-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="8c301-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="8c301-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8c301-2770">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8c301-2770">Custom configuration provider</span></span>

<span data-ttu-id="8c301-2771">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8c301-2772">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8c301-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8c301-2773">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8c301-2774">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8c301-2775">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8c301-2776">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8c301-2777">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="8c301-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8c301-2778">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8c301-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8c301-2780">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8c301-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8c301-2782"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8c301-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8c301-2784"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8c301-2785">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8c301-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8c301-2787">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8c301-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8c301-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c301-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8c301-2789">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8c301-2790">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8c301-2790">Access configuration during startup</span></span>

<span data-ttu-id="8c301-2791">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8c301-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8c301-2792">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8c301-2793">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8c301-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8c301-2794">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8c301-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8c301-2795">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="8c301-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8c301-2796">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8c301-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8c301-2797">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="8c301-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8c301-2798">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="8c301-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="8c301-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8c301-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8c301-2800">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c301-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c301-2801">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8c301-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
