---
title: ASP.NET Core の構成
author: rick-anderson
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c6b77f20792c32a697fff8d1d78ef3c2536c5f8f
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722898"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8980d-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="8980d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8980d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8980d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8980d-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8980d-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="8980d-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8980d-107">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8980d-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-108">Environment variables</span></span>
* <span data-ttu-id="8980d-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8980d-109">Azure Key Vault</span></span>
* <span data-ttu-id="8980d-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8980d-110">Azure App Configuration</span></span>
* <span data-ttu-id="8980d-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8980d-111">Command-line arguments</span></span>
* <span data-ttu-id="8980d-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="8980d-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-113">Directory files</span></span>
* <span data-ttu-id="8980d-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="8980d-114">In-memory .NET objects</span></span>

<span data-ttu-id="8980d-115">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8980d-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8980d-116">既定の構成</span><span class="sxs-lookup"><span data-stu-id="8980d-116">Default configuration</span></span>

<span data-ttu-id="8980d-117">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="8980d-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8980d-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8980d-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8980d-120">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8980d-121">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="8980d-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8980d-122">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="8980d-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8980d-123">たとえば、*appsettings*.***Production***.*json* および  *appsettings*.***Development***.*json*。</span><span class="sxs-lookup"><span data-stu-id="8980d-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="8980d-124">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8980d-125">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8980d-126">[コマンドライン構成プロバイダー](#command-line)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8980d-127">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8980d-128">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8980d-129">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#clcp) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="8980d-130">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8980d-131">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="8980d-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8980d-132">appsettings.json</span></span>

<span data-ttu-id="8980d-133">以下の *appsettings.json* ファイルについて考えます:</span><span class="sxs-lookup"><span data-stu-id="8980d-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8980d-134">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-135">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="8980d-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="8980d-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8980d-136">*appsettings.json*</span></span>
1. <span data-ttu-id="8980d-137">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production***.*json* および *appsettings*.***Development***.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="8980d-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="8980d-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8980d-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8980d-140">*appsettings*.`Environment`.*json* の値は、*appsettings. json*のキーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8980d-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="8980d-141">For example, by default:</span></span>

* <span data-ttu-id="8980d-142">開発においては、*appsettings*.***Development***.*json* 構成が *appsettings.json* の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="8980d-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8980d-143">運用環境では、*appsettings*.***Production***.*json* 構成が *appsettings. json*の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="8980d-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8980d-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="8980d-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8980d-145">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="8980d-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8980d-146">[既定の](#default)構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効化できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8980d-147">アプリの***開始後***に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8980d-148">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="8980d-149">サービス コレクションの結合</span><span class="sxs-lookup"><span data-stu-id="8980d-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8980d-150">セキュリティとシークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="8980d-150">Security and secret manager</span></span>

<span data-ttu-id="8980d-151">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="8980d-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="8980d-152">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8980d-153">[シークレット マネージャー](xref:security/app-secrets) を使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8980d-154">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8980d-155">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8980d-156">[既定](#default)では、[シークレット マネージャー](xref:security/app-secrets)は*appsettings.json* と *appsettings.* `Environment` *.json* の後に構成設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8980d-157">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="8980d-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8980d-158"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8980d-159">シークレット マネージャーでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8980d-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8980d-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8980d-161">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8980d-162">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-162">Environment variables</span></span>

<span data-ttu-id="8980d-163">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> は *appsettings.json*、*appsettings.* `Environment` *.json*、および [シークレット マネージャー](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8980d-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8980d-164">そのため、環境から読み取られたキー値は、*appsettings.json*、*appsettings.* `Environment` *.json*、シークレット マネージャーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8980d-165">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="8980d-165">The following `set` commands:</span></span>

* <span data-ttu-id="8980d-166">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8980d-167">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="8980d-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8980d-168">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8980d-169">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="8980d-169">The preceding environment settings:</span></span>

* <span data-ttu-id="8980d-170">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="8980d-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8980d-171">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="8980d-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8980d-172">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8980d-173">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8980d-174">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8980d-175">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8980d-176">上記のコマンドが *appsettings.json* と *appsettings.* `Environment` *.json* をオーバーライドすることをテストするには:</span><span class="sxs-lookup"><span data-stu-id="8980d-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8980d-177">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="8980d-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8980d-178">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="8980d-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8980d-179">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="8980d-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8980d-180">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8980d-180">In the preceding code:</span></span>

* <span data-ttu-id="8980d-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8980d-182">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8980d-183">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8980d-184">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8980d-185">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8980d-186">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="8980d-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8980d-187">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8980d-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8980d-188">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8980d-189">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8980d-190">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。</span><span class="sxs-lookup"><span data-stu-id="8980d-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8980d-191">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="8980d-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8980d-192">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8980d-193">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-193">Exposed as environment variables.</span></span>

<span data-ttu-id="8980d-194">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8980d-195">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="8980d-196">launchSettings.json で設定された環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="8980d-197">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8980d-198">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="8980d-198">Command-line</span></span>

<span data-ttu-id="8980d-199">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="8980d-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8980d-200">*appsettings.json* と *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="8980d-200">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8980d-201">開発環境の [App シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8980d-202">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-202">Environment variables.</span></span>

<span data-ttu-id="8980d-203">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8980d-204">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8980d-204">Command-line arguments</span></span>

<span data-ttu-id="8980d-205">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8980d-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8980d-206">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8980d-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8980d-207">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="8980d-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8980d-208">キーの値:</span><span class="sxs-lookup"><span data-stu-id="8980d-208">The key value:</span></span>

* <span data-ttu-id="8980d-209">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8980d-210">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="8980d-211">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="8980d-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="8980d-212">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8980d-213">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="8980d-213">Switch mappings</span></span>

<span data-ttu-id="8980d-214">スイッチ マッピングでは、**キー**名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8980d-215"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="8980d-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8980d-216">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8980d-217">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8980d-218">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="8980d-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8980d-219">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="8980d-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8980d-220">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8980d-221">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8980d-222">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="8980d-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8980d-223">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="8980d-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-224">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="8980d-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8980d-225">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8980d-226">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8980d-227">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="8980d-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8980d-228">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="8980d-228">Hierarchical configuration data</span></span>

<span data-ttu-id="8980d-229">構成 API では、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データの読み取りが行われます。</span><span class="sxs-lookup"><span data-stu-id="8980d-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8980d-230">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *appsettings.json* 　ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8980d-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8980d-231">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8980d-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-232">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8980d-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8980d-233">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8980d-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8980d-235">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8980d-236">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="8980d-236">Configuration keys and values</span></span>

<span data-ttu-id="8980d-237">構成キー:</span><span class="sxs-lookup"><span data-stu-id="8980d-237">Configuration keys:</span></span>

* <span data-ttu-id="8980d-238">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-238">Are case-insensitive.</span></span> <span data-ttu-id="8980d-239">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8980d-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8980d-240">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8980d-241">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8980d-242">階層キー</span><span class="sxs-lookup"><span data-stu-id="8980d-242">Hierarchical keys</span></span>
  * <span data-ttu-id="8980d-243">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="8980d-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8980d-244">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8980d-245">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8980d-246">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8980d-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8980d-247">シークレットがアプリの構成に読み込まれると、[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)によって `--` が `:` に自動的に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8980d-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8980d-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8980d-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8980d-249">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8980d-250">構成値:</span><span class="sxs-lookup"><span data-stu-id="8980d-250">Configuration values:</span></span>

* <span data-ttu-id="8980d-251">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="8980d-251">Are strings.</span></span>
* <span data-ttu-id="8980d-252">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8980d-253">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-253">Configuration providers</span></span>

<span data-ttu-id="8980d-254">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8980d-255">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-255">Provider</span></span> | <span data-ttu-id="8980d-256">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="8980d-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="8980d-257">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="8980d-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8980d-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="8980d-259">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="8980d-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8980d-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="8980d-261">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="8980d-262">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="8980d-262">Command-line parameters</span></span> |
| [<span data-ttu-id="8980d-263">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8980d-264">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="8980d-264">Custom source</span></span> |
| [<span data-ttu-id="8980d-265">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="8980d-266">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-266">Environment variables</span></span> |
| [<span data-ttu-id="8980d-267">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8980d-268">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="8980d-269">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8980d-270">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-270">Directory files</span></span> |
| [<span data-ttu-id="8980d-271">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8980d-272">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="8980d-272">In-memory collections</span></span> |
| [<span data-ttu-id="8980d-273">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="8980d-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="8980d-274">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-274">File in the user profile directory</span></span> |

<span data-ttu-id="8980d-275">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8980d-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8980d-276">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8980d-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8980d-277">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8980d-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8980d-278">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8980d-278">*appsettings.json*</span></span>
1. <span data-ttu-id="8980d-279">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="8980d-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8980d-280">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="8980d-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8980d-281">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8980d-282">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8980d-283">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="8980d-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8980d-284">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8980d-285">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8980d-285">Connection string prefixes</span></span>

<span data-ttu-id="8980d-286">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="8980d-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8980d-287">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8980d-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8980d-288">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8980d-289">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8980d-289">Connection string prefix</span></span> | <span data-ttu-id="8980d-290">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8980d-291">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8980d-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="8980d-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8980d-293">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8980d-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8980d-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8980d-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8980d-295">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="8980d-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8980d-296">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8980d-297">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="8980d-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8980d-298">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="8980d-298">Environment variable key</span></span> | <span data-ttu-id="8980d-299">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="8980d-299">Converted configuration key</span></span> | <span data-ttu-id="8980d-300">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="8980d-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-301">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-302">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-303">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-304">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-305">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-306">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-307">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8980d-308">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-308">File configuration provider</span></span>

<span data-ttu-id="8980d-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="8980d-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8980d-310">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="8980d-310">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8980d-311">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-311">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8980d-312">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-312">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8980d-313">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-313">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8980d-314">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-314">INI configuration provider</span></span>

<span data-ttu-id="8980d-315"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-315">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-316">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8980d-316">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8980d-317">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="8980d-317">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8980d-318">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-318">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8980d-319">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8980d-319">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8980d-320">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8980d-320">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8980d-321">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-321">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8980d-322">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-322">JSON configuration provider</span></span>

<span data-ttu-id="8980d-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8980d-324">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="8980d-324">Overloads can specify:</span></span>

* <span data-ttu-id="8980d-325">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-325">Whether the file is optional.</span></span>
* <span data-ttu-id="8980d-326">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8980d-327">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="8980d-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8980d-328">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8980d-328">The preceding code:</span></span>

* <span data-ttu-id="8980d-329">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="8980d-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8980d-330">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="8980d-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8980d-331">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8980d-332">*MyConfig.json* ファイルの前に[既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8980d-333">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="8980d-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8980d-334">通常は、[環境変数構成プロバイダー](#evcp)および[コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="8980d-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8980d-335">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8980d-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8980d-336">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="8980d-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8980d-337">*appsettings.json* と *appsettings*.`Environment`.*json* ファイルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8980d-338">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8980d-339">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8980d-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8980d-340">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8980d-341">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-341">XML configuration provider</span></span>

<span data-ttu-id="8980d-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-343">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="8980d-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8980d-344">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="8980d-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8980d-345">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8980d-346">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8980d-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8980d-347">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="8980d-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8980d-348">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-349">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="8980d-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8980d-350">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8980d-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-351">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8980d-352">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8980d-353">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8980d-353">key:attribute</span></span>
* <span data-ttu-id="8980d-354">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8980d-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8980d-355">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="8980d-356"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8980d-357">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8980d-357">The key is the file name.</span></span> <span data-ttu-id="8980d-358">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-358">The value contains the file's contents.</span></span> <span data-ttu-id="8980d-359">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8980d-360">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8980d-361">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8980d-362">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="8980d-363">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="8980d-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8980d-364">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="8980d-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8980d-365">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8980d-366">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8980d-367">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8980d-368">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-368">Memory configuration provider</span></span>

<span data-ttu-id="8980d-369"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8980d-370">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="8980d-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8980d-371">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8980d-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-372">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8980d-373">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8980d-374">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8980d-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="8980d-375">GetValue</span></span>

<span data-ttu-id="8980d-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="8980d-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-377">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8980d-378">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="8980d-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8980d-379">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="8980d-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8980d-380">以下のコードでは、*MySubsection セクション*を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="8980d-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8980d-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="8980d-381">GetSection</span></span>

<span data-ttu-id="8980d-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="8980d-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8980d-383">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-384">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-385">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8980d-386">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8980d-387">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8980d-388"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8980d-389">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="8980d-389">GetChildren and Exists</span></span>

<span data-ttu-id="8980d-390">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-391">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="8980d-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8980d-392">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="8980d-392">Bind an array</span></span>

<span data-ttu-id="8980d-393">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8980d-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8980d-394">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8980d-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8980d-395">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="8980d-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8980d-396">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="8980d-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8980d-397">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8980d-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-398">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8980d-399">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="8980d-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8980d-400">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="8980d-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8980d-401">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="8980d-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8980d-402">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="8980d-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8980d-403">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8980d-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-404">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8980d-405">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8980d-406">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8980d-407">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8980d-408">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="8980d-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8980d-409">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="8980d-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8980d-410">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="8980d-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8980d-411">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="8980d-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-412">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="8980d-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8980d-413">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8980d-414">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-414">Custom configuration provider</span></span>

<span data-ttu-id="8980d-415">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8980d-416">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8980d-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8980d-417">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8980d-418">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8980d-419">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8980d-420">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="8980d-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8980d-421">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8980d-422">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="8980d-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8980d-423">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8980d-424">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8980d-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8980d-426"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8980d-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8980d-428"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8980d-429">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8980d-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8980d-430">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8980d-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8980d-432">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8980d-433">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8980d-434">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8980d-435">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="8980d-435">Access configuration in Startup</span></span>

<span data-ttu-id="8980d-436">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8980d-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8980d-437">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="8980d-438">Razor ページの構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8980d-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8980d-439">次のコードでは Razor ページの構成データが表示されます:</span><span class="sxs-lookup"><span data-stu-id="8980d-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8980d-440">次のコードでは、`MyOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="8980d-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8980d-441">次のマークアップは、[`@inject`](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、オプションの値を解決して表示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8980d-442">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="8980d-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8980d-443">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="8980d-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8980d-444">デリゲートでオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="8980d-444">Configure options with a delegate</span></span>

<span data-ttu-id="8980d-445">デリゲートで構成されたオプションは、構成プロバイダーで設定された値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8980d-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8980d-446">サンプル アプリの例 2 では、デリゲートでオプションを構成しています。</span><span class="sxs-lookup"><span data-stu-id="8980d-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8980d-447">次のコードでは、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8980d-448">デリゲートを利用して `MyOptions` の値が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8980d-449">このコードには、次のオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8980d-450">先の例では、値 `Option1` と `Option2` が *appsettings.json* で指定されてから、構成されているデリゲートによりオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8980d-451">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8980d-451">Host versus app configuration</span></span>

<span data-ttu-id="8980d-452">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8980d-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8980d-453">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8980d-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8980d-454">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8980d-455">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8980d-456">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8980d-457">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="8980d-457">Default host configuration</span></span>

<span data-ttu-id="8980d-458">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8980d-459">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8980d-460">[環境変数構成プロバイダー](#environment-variables)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="8980d-461">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8980d-462">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8980d-463">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="8980d-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8980d-464">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8980d-465">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8980d-466">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8980d-467">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8980d-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8980d-468">その他の構成</span><span class="sxs-lookup"><span data-stu-id="8980d-468">Other configuration</span></span>

<span data-ttu-id="8980d-469">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="8980d-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8980d-470">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8980d-471">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8980d-472"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="8980d-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8980d-473">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="8980d-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8980d-474">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8980d-475">*launchSettings.json* に設定されている環境変数で、システム環境に設定されているそれらがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-475">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="8980d-476">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-476">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8980d-477">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="8980d-477">Add configuration from an external assembly</span></span>

<span data-ttu-id="8980d-478"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8980d-478">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8980d-479">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-479">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8980d-480">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8980d-480">Additional resources</span></span>

* [<span data-ttu-id="8980d-481">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="8980d-481">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8980d-482">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="8980d-482">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8980d-483">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-483">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8980d-484">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8980d-484">Azure Key Vault</span></span>
* <span data-ttu-id="8980d-485">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8980d-485">Azure App Configuration</span></span>
* <span data-ttu-id="8980d-486">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8980d-486">Command-line arguments</span></span>
* <span data-ttu-id="8980d-487">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="8980d-487">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8980d-488">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-488">Directory files</span></span>
* <span data-ttu-id="8980d-489">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-489">Environment variables</span></span>
* <span data-ttu-id="8980d-490">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="8980d-490">In-memory .NET objects</span></span>
* <span data-ttu-id="8980d-491">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-491">Settings files</span></span>

<span data-ttu-id="8980d-492">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-492">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8980d-493">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="8980d-493">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8980d-494">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="8980d-494">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8980d-495">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="8980d-495">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8980d-496">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-496">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8980d-497">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8980d-497">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8980d-498">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8980d-498">Host versus app configuration</span></span>

<span data-ttu-id="8980d-499">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8980d-499">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8980d-500">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8980d-500">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8980d-501">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-501">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8980d-502">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-502">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8980d-503">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-503">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8980d-504">その他の構成</span><span class="sxs-lookup"><span data-stu-id="8980d-504">Other configuration</span></span>

<span data-ttu-id="8980d-505">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="8980d-505">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8980d-506">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-506">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8980d-507">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-507">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8980d-508"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="8980d-508">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8980d-509">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="8980d-509">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8980d-510">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-510">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8980d-511">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-511">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8980d-512">既定の構成</span><span class="sxs-lookup"><span data-stu-id="8980d-512">Default configuration</span></span>

<span data-ttu-id="8980d-513">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-513">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8980d-514">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-514">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8980d-515">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-515">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8980d-516">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-516">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8980d-517">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-517">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8980d-518">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-518">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8980d-519">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-519">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8980d-520">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-520">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8980d-521">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-521">App configuration is provided from:</span></span>
  * <span data-ttu-id="8980d-522">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="8980d-522">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8980d-523">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8980d-523">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8980d-524">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-524">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8980d-525">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-525">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8980d-526">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8980d-527">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="8980d-527">Security</span></span>

<span data-ttu-id="8980d-528">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="8980d-528">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8980d-529">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-529">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8980d-530">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-530">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8980d-531">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-531">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8980d-532">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-532">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8980d-533"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-533"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8980d-534">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8980d-534">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8980d-535">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-535">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8980d-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8980d-537">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-537">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8980d-538">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="8980d-538">Hierarchical configuration data</span></span>

<span data-ttu-id="8980d-539">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-539">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8980d-540">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8980d-540">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8980d-541">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-541">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8980d-542">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-542">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8980d-543">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-543">section0:key0</span></span>
* <span data-ttu-id="8980d-544">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-544">section0:key1</span></span>
* <span data-ttu-id="8980d-545">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-545">section1:key0</span></span>
* <span data-ttu-id="8980d-546">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-546">section1:key1</span></span>

<span data-ttu-id="8980d-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8980d-548">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-548">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8980d-549">規約</span><span class="sxs-lookup"><span data-stu-id="8980d-549">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8980d-550">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-550">Sources and providers</span></span>

<span data-ttu-id="8980d-551">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8980d-551">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8980d-552">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="8980d-552">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8980d-553">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="8980d-553">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8980d-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8980d-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8980d-556">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-556">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8980d-557">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="8980d-557">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8980d-558">キー</span><span class="sxs-lookup"><span data-stu-id="8980d-558">Keys</span></span>

<span data-ttu-id="8980d-559">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-559">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8980d-560">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-560">Keys are case-insensitive.</span></span> <span data-ttu-id="8980d-561">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8980d-561">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8980d-562">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="8980d-562">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="8980d-563">重複する JSON キーの詳細については、「[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/2381)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-563">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="8980d-564">階層キー</span><span class="sxs-lookup"><span data-stu-id="8980d-564">Hierarchical keys</span></span>
  * <span data-ttu-id="8980d-565">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="8980d-565">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8980d-566">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-566">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8980d-567">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-567">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8980d-568">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8980d-568">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8980d-569">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8980d-569">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8980d-570"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8980d-570">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8980d-571">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-571">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8980d-572">値</span><span class="sxs-lookup"><span data-stu-id="8980d-572">Values</span></span>

<span data-ttu-id="8980d-573">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-573">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8980d-574">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="8980d-574">Values are strings.</span></span>
* <span data-ttu-id="8980d-575">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-575">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8980d-576">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-576">Providers</span></span>

<span data-ttu-id="8980d-577">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-577">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8980d-578">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-578">Provider</span></span> | <span data-ttu-id="8980d-579">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="8980d-579">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8980d-580">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="8980d-580">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8980d-581">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8980d-581">Azure Key Vault</span></span> |
| <span data-ttu-id="8980d-582">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="8980d-582">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8980d-583">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8980d-583">Azure App Configuration</span></span> |
| [<span data-ttu-id="8980d-584">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-584">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8980d-585">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="8980d-585">Command-line parameters</span></span> |
| [<span data-ttu-id="8980d-586">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-586">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8980d-587">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="8980d-587">Custom source</span></span> |
| [<span data-ttu-id="8980d-588">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-588">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8980d-589">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-589">Environment variables</span></span> |
| [<span data-ttu-id="8980d-590">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-590">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8980d-591">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="8980d-591">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8980d-592">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-592">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8980d-593">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-593">Directory files</span></span> |
| [<span data-ttu-id="8980d-594">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-594">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8980d-595">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="8980d-595">In-memory collections</span></span> |
| <span data-ttu-id="8980d-596">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="8980d-596">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8980d-597">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="8980d-597">File in the user profile directory</span></span> |

<span data-ttu-id="8980d-598">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8980d-598">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8980d-599">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="8980d-599">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8980d-600">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8980d-600">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8980d-601">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8980d-601">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8980d-602">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="8980d-602">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8980d-603">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8980d-603">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8980d-604">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="8980d-604">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8980d-605">環境変数</span><span class="sxs-lookup"><span data-stu-id="8980d-605">Environment variables</span></span>
1. <span data-ttu-id="8980d-606">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8980d-606">Command-line arguments</span></span>

<span data-ttu-id="8980d-607">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="8980d-607">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8980d-608">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-608">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8980d-609">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-609">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8980d-610">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="8980d-610">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8980d-611">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-611">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8980d-612">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8980d-612">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8980d-613">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-613">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8980d-614">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="8980d-614">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8980d-615">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-615">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8980d-616">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="8980d-616">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8980d-617">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-617">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8980d-618">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="8980d-618">Consume configuration during app startup</span></span>

<span data-ttu-id="8980d-619">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-619">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8980d-620">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-620">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8980d-621">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-621">Command-line Configuration Provider</span></span>

<span data-ttu-id="8980d-622"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-622">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-623">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-623">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8980d-624">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-624">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8980d-625">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-625">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8980d-626">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-626">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8980d-627">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8980d-627">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8980d-628">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-628">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8980d-629">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-629">Environment variables.</span></span>

<span data-ttu-id="8980d-630">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-630">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8980d-631">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-631">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8980d-632">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="8980d-632">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8980d-633">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-633">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8980d-634">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="8980d-634">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8980d-635">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-635">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8980d-636">**例**</span><span class="sxs-lookup"><span data-stu-id="8980d-636">**Example**</span></span>

<span data-ttu-id="8980d-637">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-637">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8980d-638">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="8980d-638">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8980d-639">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="8980d-639">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8980d-640">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8980d-640">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8980d-641">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8980d-641">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8980d-642">引数</span><span class="sxs-lookup"><span data-stu-id="8980d-642">Arguments</span></span>

<span data-ttu-id="8980d-643">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-643">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8980d-644">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-644">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8980d-645">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8980d-645">Key prefix</span></span>               | <span data-ttu-id="8980d-646">例</span><span class="sxs-lookup"><span data-stu-id="8980d-646">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8980d-647">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="8980d-647">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8980d-648">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="8980d-648">Two dashes (`--`)</span></span>        | <span data-ttu-id="8980d-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8980d-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8980d-650">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="8980d-650">Forward slash (`/`)</span></span>      | <span data-ttu-id="8980d-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8980d-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8980d-652">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-652">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8980d-653">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="8980d-653">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8980d-654">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="8980d-654">Switch mappings</span></span>

<span data-ttu-id="8980d-655">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-655">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8980d-656"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-656">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8980d-657">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-657">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8980d-658">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-658">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8980d-659">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="8980d-659">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8980d-660">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="8980d-660">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8980d-661">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-661">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8980d-662">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-662">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8980d-663">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-663">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8980d-664">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-664">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8980d-665">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-665">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8980d-666">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8980d-666">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8980d-667">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-667">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8980d-668">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-668">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8980d-669">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-669">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8980d-670">Key</span><span class="sxs-lookup"><span data-stu-id="8980d-670">Key</span></span>       | <span data-ttu-id="8980d-671">値</span><span class="sxs-lookup"><span data-stu-id="8980d-671">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8980d-672">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-672">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8980d-673">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-673">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8980d-674">Key</span><span class="sxs-lookup"><span data-stu-id="8980d-674">Key</span></span>               | <span data-ttu-id="8980d-675">[値]</span><span class="sxs-lookup"><span data-stu-id="8980d-675">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8980d-676">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-676">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8980d-677"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-677">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-678">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-678">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8980d-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8980d-680">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-680">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8980d-681">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-681">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8980d-682">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-682">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8980d-683">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-683">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8980d-684">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="8980d-684">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8980d-685">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8980d-685">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8980d-686">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-686">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8980d-687">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-687">Command-line arguments.</span></span>

<span data-ttu-id="8980d-688">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-688">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8980d-689">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-689">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8980d-690">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-690">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8980d-691">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="8980d-691">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8980d-692">**例**</span><span class="sxs-lookup"><span data-stu-id="8980d-692">**Example**</span></span>

<span data-ttu-id="8980d-693">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-693">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8980d-694">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8980d-694">Run the sample app.</span></span> <span data-ttu-id="8980d-695">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8980d-695">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8980d-696">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8980d-696">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8980d-697">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="8980d-697">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8980d-698">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-698">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8980d-699">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-699">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8980d-700">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="8980d-700">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8980d-701">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="8980d-701">Prefixes</span></span>

<span data-ttu-id="8980d-702">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-702">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8980d-703">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-703">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8980d-704">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-704">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8980d-705">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-705">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8980d-706">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-706">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8980d-707">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="8980d-707">**Connection string prefixes**</span></span>

<span data-ttu-id="8980d-708">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-708">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8980d-709">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-709">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8980d-710">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8980d-710">Connection string prefix</span></span> | <span data-ttu-id="8980d-711">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-711">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8980d-712">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-712">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8980d-713">MySQL</span><span class="sxs-lookup"><span data-stu-id="8980d-713">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8980d-714">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8980d-714">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8980d-715">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8980d-715">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8980d-716">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="8980d-716">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8980d-717">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-717">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8980d-718">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="8980d-718">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8980d-719">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="8980d-719">Environment variable key</span></span> | <span data-ttu-id="8980d-720">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="8980d-720">Converted configuration key</span></span> | <span data-ttu-id="8980d-721">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="8980d-721">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-722">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-722">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-723">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-723">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-724">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-724">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-725">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-726">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-726">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8980d-727">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8980d-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8980d-728">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8980d-728">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="8980d-729">**例**</span><span class="sxs-lookup"><span data-stu-id="8980d-729">**Example**</span></span>

<span data-ttu-id="8980d-730">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-730">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8980d-731">名前: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8980d-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8980d-732">値: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8980d-732">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8980d-733">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-733">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8980d-734">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-734">File Configuration Provider</span></span>

<span data-ttu-id="8980d-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="8980d-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8980d-736">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="8980d-736">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8980d-737">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-737">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8980d-738">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-738">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8980d-739">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-739">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8980d-740">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-740">INI Configuration Provider</span></span>

<span data-ttu-id="8980d-741"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-741">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-742">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-742">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8980d-743">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-743">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8980d-744">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-744">Overloads permit specifying:</span></span>

* <span data-ttu-id="8980d-745">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-745">Whether the file is optional.</span></span>
* <span data-ttu-id="8980d-746">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-746">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8980d-747">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-747">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8980d-748">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-748">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8980d-749">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="8980d-749">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8980d-750">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-750">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8980d-751">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-751">section0:key0</span></span>
* <span data-ttu-id="8980d-752">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-752">section0:key1</span></span>
* <span data-ttu-id="8980d-753">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8980d-753">section1:subsection:key</span></span>
* <span data-ttu-id="8980d-754">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8980d-754">section2:subsection0:key</span></span>
* <span data-ttu-id="8980d-755">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8980d-755">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8980d-756">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-756">JSON Configuration Provider</span></span>

<span data-ttu-id="8980d-757"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-757">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8980d-758">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-758">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8980d-759">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-759">Overloads permit specifying:</span></span>

* <span data-ttu-id="8980d-760">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-760">Whether the file is optional.</span></span>
* <span data-ttu-id="8980d-761">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-761">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8980d-762">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-762">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8980d-763">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-763">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8980d-764">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-764">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8980d-765">*appsettings.json*:このファイルは最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8980d-765">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="8980d-766">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8980d-766">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8980d-767">*appsettings.{Environment}.json*:ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-767">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8980d-768">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-768">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8980d-769">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-769">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8980d-770">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8980d-770">Environment variables.</span></span>
* <span data-ttu-id="8980d-771">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8980d-771">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8980d-772">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8980d-772">Command-line arguments.</span></span>

<span data-ttu-id="8980d-773">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-773">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8980d-774">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-774">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8980d-775">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-775">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8980d-776">**例**</span><span class="sxs-lookup"><span data-stu-id="8980d-776">**Example**</span></span>

<span data-ttu-id="8980d-777">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-777">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8980d-778">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8980d-778">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8980d-779">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8980d-779">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8980d-780">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-780">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8980d-781">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8980d-781">Run the sample app.</span></span> <span data-ttu-id="8980d-782">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8980d-782">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8980d-783">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8980d-783">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8980d-784">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="8980d-784">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8980d-785">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8980d-785">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8980d-786">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="8980d-786">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8980d-787">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="8980d-787">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8980d-788">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8980d-788">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8980d-789">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8980d-789">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8980d-790">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="8980d-790">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8980d-791">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-791">XML Configuration Provider</span></span>

<span data-ttu-id="8980d-792"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-792">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8980d-793">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-793">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8980d-794">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-794">Overloads permit specifying:</span></span>

* <span data-ttu-id="8980d-795">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-795">Whether the file is optional.</span></span>
* <span data-ttu-id="8980d-796">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8980d-796">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8980d-797">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-797">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8980d-798">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-798">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8980d-799">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="8980d-799">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8980d-800">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-800">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8980d-801">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-801">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8980d-802">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-802">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8980d-803">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-803">section0:key0</span></span>
* <span data-ttu-id="8980d-804">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-804">section0:key1</span></span>
* <span data-ttu-id="8980d-805">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-805">section1:key0</span></span>
* <span data-ttu-id="8980d-806">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-806">section1:key1</span></span>

<span data-ttu-id="8980d-807">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="8980d-807">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8980d-808">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8980d-809">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-809">section:section0:key:key0</span></span>
* <span data-ttu-id="8980d-810">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-810">section:section0:key:key1</span></span>
* <span data-ttu-id="8980d-811">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-811">section:section1:key:key0</span></span>
* <span data-ttu-id="8980d-812">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-812">section:section1:key:key1</span></span>

<span data-ttu-id="8980d-813">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-813">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8980d-814">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8980d-815">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8980d-815">key:attribute</span></span>
* <span data-ttu-id="8980d-816">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8980d-816">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8980d-817">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-817">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8980d-818"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-818">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8980d-819">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8980d-819">The key is the file name.</span></span> <span data-ttu-id="8980d-820">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-820">The value contains the file's contents.</span></span> <span data-ttu-id="8980d-821">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-821">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8980d-822">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-822">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8980d-823">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="8980d-823">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8980d-824">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-824">Overloads permit specifying:</span></span>

* <span data-ttu-id="8980d-825">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="8980d-825">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8980d-826">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="8980d-826">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8980d-827">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-827">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8980d-828">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-828">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8980d-829">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-829">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8980d-830">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-830">Memory Configuration Provider</span></span>

<span data-ttu-id="8980d-831"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-831">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8980d-832">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-832">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8980d-833">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-833">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8980d-834">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8980d-835">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-835">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8980d-836">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-836">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8980d-837">GetValue</span><span class="sxs-lookup"><span data-stu-id="8980d-837">GetValue</span></span>

<span data-ttu-id="8980d-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="8980d-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8980d-839">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8980d-839">An overload accepts a default value.</span></span>

<span data-ttu-id="8980d-840">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="8980d-840">The following example:</span></span>

* <span data-ttu-id="8980d-841">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="8980d-841">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8980d-842">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-842">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8980d-843">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="8980d-843">Types the value as an `int`.</span></span>
* <span data-ttu-id="8980d-844">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="8980d-844">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8980d-845">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="8980d-845">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8980d-846">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="8980d-846">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8980d-847">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8980d-847">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8980d-848">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-848">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8980d-849">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-849">section0:key0</span></span>
* <span data-ttu-id="8980d-850">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-850">section0:key1</span></span>
* <span data-ttu-id="8980d-851">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-851">section1:key0</span></span>
* <span data-ttu-id="8980d-852">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-852">section1:key1</span></span>
* <span data-ttu-id="8980d-853">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-853">section2:subsection0:key0</span></span>
* <span data-ttu-id="8980d-854">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-854">section2:subsection0:key1</span></span>
* <span data-ttu-id="8980d-855">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8980d-855">section2:subsection1:key0</span></span>
* <span data-ttu-id="8980d-856">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8980d-856">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8980d-857">GetSection</span><span class="sxs-lookup"><span data-stu-id="8980d-857">GetSection</span></span>

<span data-ttu-id="8980d-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8980d-859">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-859">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8980d-860">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-860">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8980d-861">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-861">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8980d-862">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-862">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8980d-863">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-863">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8980d-864">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-864">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8980d-865"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8980d-866">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8980d-866">GetChildren</span></span>

<span data-ttu-id="8980d-867">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-867">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8980d-868">既存</span><span class="sxs-lookup"><span data-stu-id="8980d-868">Exists</span></span>

<span data-ttu-id="8980d-869">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8980d-869">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8980d-870">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="8980d-870">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8980d-871">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="8980d-871">Bind to an object graph</span></span>

<span data-ttu-id="8980d-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8980d-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8980d-873">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-873">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8980d-874">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8980d-874">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8980d-875">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-875">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8980d-876">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-876">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8980d-877">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8980d-877">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8980d-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="8980d-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8980d-879">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="8980d-879">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8980d-880">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="8980d-880">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8980d-881">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8980d-881">Bind an array to a class</span></span>

<span data-ttu-id="8980d-882">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="8980d-882">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8980d-883"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8980d-883">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8980d-884">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8980d-884">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8980d-885">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-885">Binding is provided by convention.</span></span> <span data-ttu-id="8980d-886">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-886">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8980d-887">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8980d-887">**In-memory array processing**</span></span>

<span data-ttu-id="8980d-888">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="8980d-888">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8980d-889">Key</span><span class="sxs-lookup"><span data-stu-id="8980d-889">Key</span></span>             | <span data-ttu-id="8980d-890">[値]</span><span class="sxs-lookup"><span data-stu-id="8980d-890">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8980d-891">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="8980d-891">array:entries:0</span></span> | <span data-ttu-id="8980d-892">value0</span><span class="sxs-lookup"><span data-stu-id="8980d-892">value0</span></span> |
| <span data-ttu-id="8980d-893">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="8980d-893">array:entries:1</span></span> | <span data-ttu-id="8980d-894">value1</span><span class="sxs-lookup"><span data-stu-id="8980d-894">value1</span></span> |
| <span data-ttu-id="8980d-895">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="8980d-895">array:entries:2</span></span> | <span data-ttu-id="8980d-896">value2</span><span class="sxs-lookup"><span data-stu-id="8980d-896">value2</span></span> |
| <span data-ttu-id="8980d-897">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="8980d-897">array:entries:4</span></span> | <span data-ttu-id="8980d-898">value4</span><span class="sxs-lookup"><span data-stu-id="8980d-898">value4</span></span> |
| <span data-ttu-id="8980d-899">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="8980d-899">array:entries:5</span></span> | <span data-ttu-id="8980d-900">value5</span><span class="sxs-lookup"><span data-stu-id="8980d-900">value5</span></span> |

<span data-ttu-id="8980d-901">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-901">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8980d-902">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="8980d-902">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8980d-903">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="8980d-903">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8980d-904">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-904">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8980d-905">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8980d-905">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8980d-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="8980d-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8980d-907">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-907">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8980d-908">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8980d-908">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8980d-909">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8980d-909">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8980d-910">0</span><span class="sxs-lookup"><span data-stu-id="8980d-910">0</span></span>                            | <span data-ttu-id="8980d-911">value0</span><span class="sxs-lookup"><span data-stu-id="8980d-911">value0</span></span>                       |
| <span data-ttu-id="8980d-912">1</span><span class="sxs-lookup"><span data-stu-id="8980d-912">1</span></span>                            | <span data-ttu-id="8980d-913">value1</span><span class="sxs-lookup"><span data-stu-id="8980d-913">value1</span></span>                       |
| <span data-ttu-id="8980d-914">2</span><span class="sxs-lookup"><span data-stu-id="8980d-914">2</span></span>                            | <span data-ttu-id="8980d-915">value2</span><span class="sxs-lookup"><span data-stu-id="8980d-915">value2</span></span>                       |
| <span data-ttu-id="8980d-916">3</span><span class="sxs-lookup"><span data-stu-id="8980d-916">3</span></span>                            | <span data-ttu-id="8980d-917">value4</span><span class="sxs-lookup"><span data-stu-id="8980d-917">value4</span></span>                       |
| <span data-ttu-id="8980d-918">4</span><span class="sxs-lookup"><span data-stu-id="8980d-918">4</span></span>                            | <span data-ttu-id="8980d-919">value5</span><span class="sxs-lookup"><span data-stu-id="8980d-919">value5</span></span>                       |

<span data-ttu-id="8980d-920">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-920">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8980d-921">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-921">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8980d-922">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8980d-922">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8980d-923">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-923">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8980d-924">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="8980d-924">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8980d-925">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8980d-925">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8980d-926">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="8980d-926">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8980d-927">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-927">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8980d-928">Key</span><span class="sxs-lookup"><span data-stu-id="8980d-928">Key</span></span>             | <span data-ttu-id="8980d-929">値</span><span class="sxs-lookup"><span data-stu-id="8980d-929">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8980d-930">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8980d-930">array:entries:3</span></span> | <span data-ttu-id="8980d-931">value3</span><span class="sxs-lookup"><span data-stu-id="8980d-931">value3</span></span> |

<span data-ttu-id="8980d-932">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8980d-932">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8980d-933">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8980d-933">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8980d-934">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8980d-934">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8980d-935">0</span><span class="sxs-lookup"><span data-stu-id="8980d-935">0</span></span>                            | <span data-ttu-id="8980d-936">value0</span><span class="sxs-lookup"><span data-stu-id="8980d-936">value0</span></span>                       |
| <span data-ttu-id="8980d-937">1</span><span class="sxs-lookup"><span data-stu-id="8980d-937">1</span></span>                            | <span data-ttu-id="8980d-938">value1</span><span class="sxs-lookup"><span data-stu-id="8980d-938">value1</span></span>                       |
| <span data-ttu-id="8980d-939">2</span><span class="sxs-lookup"><span data-stu-id="8980d-939">2</span></span>                            | <span data-ttu-id="8980d-940">value2</span><span class="sxs-lookup"><span data-stu-id="8980d-940">value2</span></span>                       |
| <span data-ttu-id="8980d-941">3</span><span class="sxs-lookup"><span data-stu-id="8980d-941">3</span></span>                            | <span data-ttu-id="8980d-942">value3</span><span class="sxs-lookup"><span data-stu-id="8980d-942">value3</span></span>                       |
| <span data-ttu-id="8980d-943">4</span><span class="sxs-lookup"><span data-stu-id="8980d-943">4</span></span>                            | <span data-ttu-id="8980d-944">value4</span><span class="sxs-lookup"><span data-stu-id="8980d-944">value4</span></span>                       |
| <span data-ttu-id="8980d-945">5</span><span class="sxs-lookup"><span data-stu-id="8980d-945">5</span></span>                            | <span data-ttu-id="8980d-946">value5</span><span class="sxs-lookup"><span data-stu-id="8980d-946">value5</span></span>                       |

<span data-ttu-id="8980d-947">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8980d-947">**JSON array processing**</span></span>

<span data-ttu-id="8980d-948">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-948">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8980d-949">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="8980d-949">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8980d-950">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-950">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8980d-951">Key</span><span class="sxs-lookup"><span data-stu-id="8980d-951">Key</span></span>                     | <span data-ttu-id="8980d-952">値</span><span class="sxs-lookup"><span data-stu-id="8980d-952">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8980d-953">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8980d-953">json_array:key</span></span>          | <span data-ttu-id="8980d-954">valueA</span><span class="sxs-lookup"><span data-stu-id="8980d-954">valueA</span></span> |
| <span data-ttu-id="8980d-955">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8980d-955">json_array:subsection:0</span></span> | <span data-ttu-id="8980d-956">valueB</span><span class="sxs-lookup"><span data-stu-id="8980d-956">valueB</span></span> |
| <span data-ttu-id="8980d-957">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8980d-957">json_array:subsection:1</span></span> | <span data-ttu-id="8980d-958">valueC</span><span class="sxs-lookup"><span data-stu-id="8980d-958">valueC</span></span> |
| <span data-ttu-id="8980d-959">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8980d-959">json_array:subsection:2</span></span> | <span data-ttu-id="8980d-960">valueD</span><span class="sxs-lookup"><span data-stu-id="8980d-960">valueD</span></span> |

<span data-ttu-id="8980d-961">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-961">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8980d-962">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="8980d-962">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8980d-963">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-963">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8980d-964">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="8980d-964">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8980d-965">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="8980d-965">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8980d-966">0</span><span class="sxs-lookup"><span data-stu-id="8980d-966">0</span></span>                                   | <span data-ttu-id="8980d-967">valueB</span><span class="sxs-lookup"><span data-stu-id="8980d-967">valueB</span></span>                              |
| <span data-ttu-id="8980d-968">1</span><span class="sxs-lookup"><span data-stu-id="8980d-968">1</span></span>                                   | <span data-ttu-id="8980d-969">valueC</span><span class="sxs-lookup"><span data-stu-id="8980d-969">valueC</span></span>                              |
| <span data-ttu-id="8980d-970">2</span><span class="sxs-lookup"><span data-stu-id="8980d-970">2</span></span>                                   | <span data-ttu-id="8980d-971">valueD</span><span class="sxs-lookup"><span data-stu-id="8980d-971">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8980d-972">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8980d-972">Custom configuration provider</span></span>

<span data-ttu-id="8980d-973">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-973">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8980d-974">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8980d-974">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8980d-975">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8980d-975">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8980d-976">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8980d-976">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8980d-977">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8980d-977">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8980d-978">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="8980d-978">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8980d-979">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="8980d-979">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8980d-980">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="8980d-980">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8980d-981">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-981">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8980d-982">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8980d-982">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8980d-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8980d-984"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-984">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8980d-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8980d-986"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8980d-986">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8980d-987">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8980d-987">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8980d-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8980d-989">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8980d-989">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8980d-990">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8980d-990">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8980d-991">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8980d-991">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8980d-992">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8980d-992">Access configuration during startup</span></span>

<span data-ttu-id="8980d-993">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8980d-993">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8980d-994">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8980d-994">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8980d-995">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8980d-995">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="8980d-996">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8980d-996">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8980d-997">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) 名前空間に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="8980d-997">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8980d-998">Razor Pages ページで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8980d-998">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8980d-999">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="8980d-999">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8980d-1000">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="8980d-1000">Add configuration from an external assembly</span></span>

<span data-ttu-id="8980d-1001"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8980d-1001">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8980d-1002">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8980d-1002">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8980d-1003">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8980d-1003">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end