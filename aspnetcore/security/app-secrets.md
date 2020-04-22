---
title: ASP.NETコアの開発におけるアプリの秘密の安全な保管
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして保存および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791575"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="1218f-103">ASP.NETコアの開発におけるアプリの秘密の安全な保管</span><span class="sxs-lookup"><span data-stu-id="1218f-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1218f-104">[リック・アンダーソン](https://twitter.com/RickAndMSFT) [、カーク・ラーキン](https://twitter.com/serpent5)、[ダニエル・ロス](https://github.com/danroth27)、[スコット・アディ](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="1218f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1218f-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1218f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1218f-106">このドキュメントでは、開発マシン上の ASP.NET Core アプリの開発中に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1218f-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="1218f-107">パスワードやその他の機密データをソース コードに保存しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="1218f-108">生産の秘密は、開発やテストに使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="1218f-109">シークレットはアプリと共に展開しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="1218f-110">代わりに、シークレットは、環境変数、Azure Key Vault などの制御された手段を使用して運用環境で使用できるようにする必要があります。Azure [Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを保存および保護できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="1218f-111">環境変数</span><span class="sxs-lookup"><span data-stu-id="1218f-111">Environment variables</span></span>

<span data-ttu-id="1218f-112">環境変数は、コードまたはローカル構成ファイルにアプリ シークレットが格納されないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="1218f-113">環境変数は、以前に指定したすべての構成ソースの構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="1218f-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="1218f-114">**個々のユーザー アカウント**のセキュリティが有効になっているASP.NETコア Web アプリを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1218f-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="1218f-115">既定のデータベース接続文字列は、プロジェクトの*appsettings.json*ファイルにキー`DefaultConnection`を付けて含まれています。</span><span class="sxs-lookup"><span data-stu-id="1218f-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="1218f-116">既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB 用です。</span><span class="sxs-lookup"><span data-stu-id="1218f-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="1218f-117">アプリの展開中に`DefaultConnection`、キー値を環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="1218f-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="1218f-118">環境変数には、機密性の高い資格情報を含む完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="1218f-119">環境変数は、通常、暗号化されていないプレーン テキストに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="1218f-120">マシンまたはプロセスが侵害された場合、環境変数は信頼されていないパーティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="1218f-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="1218f-121">ユーザーの秘密の漏えいを防ぐための追加措置が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="1218f-122">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="1218f-122">Secret Manager</span></span>

<span data-ttu-id="1218f-123">シークレット マネージャー ツールは、ASP.NET コア プロジェクトの開発中に機密データを格納します。</span><span class="sxs-lookup"><span data-stu-id="1218f-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="1218f-124">このコンテキストでは、機密データの一部はアプリの秘密です。</span><span class="sxs-lookup"><span data-stu-id="1218f-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="1218f-125">アプリ シークレットは、プロジェクト ツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="1218f-126">アプリ シークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクトで共有されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="1218f-127">アプリシークレットはソース管理にチェックインされません。</span><span class="sxs-lookup"><span data-stu-id="1218f-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="1218f-128">シークレット マネージャー ツールは、保存されているシークレットを暗号化しないので、信頼できるストアとして扱う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1218f-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="1218f-129">開発目的のみのためです。</span><span class="sxs-lookup"><span data-stu-id="1218f-129">It's for development purposes only.</span></span> <span data-ttu-id="1218f-130">キーと値は、ユーザー プロファイル ディレクトリの JSON 設定ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="1218f-131">シークレットマネージャーツールの仕組み</span><span class="sxs-lookup"><span data-stu-id="1218f-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="1218f-132">シークレット マネージャー ツールは、値が格納される場所や方法など、実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="1218f-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="1218f-133">これらの実装の詳細を知らなくても、このツールを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="1218f-134">値は、ローカル コンピューター上のシステムで保護されたユーザー プロファイル フォルダーに JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="1218f-135">Windows</span><span class="sxs-lookup"><span data-stu-id="1218f-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="1218f-136">ファイル システム パス:</span><span class="sxs-lookup"><span data-stu-id="1218f-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="1218f-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="1218f-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1218f-138">ファイル システム パス:</span><span class="sxs-lookup"><span data-stu-id="1218f-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="1218f-139">上記のファイル パスで`<user_secrets_id>`*、.csproj*ファイルで指定された`UserSecretsId`値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1218f-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="1218f-140">シークレット マネージャー ツールで保存されたデータの場所や形式に依存するコードを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="1218f-141">これらの実装の詳細は変更される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-141">These implementation details may change.</span></span> <span data-ttu-id="1218f-142">たとえば、シークレット値は暗号化されませんが、将来の値になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="1218f-143">シークレット ストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="1218f-143">Enable secret storage</span></span>

<span data-ttu-id="1218f-144">シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクト固有の構成設定に対して動作します。</span><span class="sxs-lookup"><span data-stu-id="1218f-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="1218f-145">シークレット マネージャー ツールには`init`、.NET Core SDK 3.0.100 以降のコマンドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1218f-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="1218f-146">ユーザー シークレットを使用するには、プロジェクト ディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="1218f-147">上記のコマンドは`UserSecretsId`*、.csproj*ファイル`PropertyGroup`内に要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="1218f-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="1218f-148">既定では、 の`UserSecretsId`内部テキストは GUID です。</span><span class="sxs-lookup"><span data-stu-id="1218f-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="1218f-149">内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="1218f-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="1218f-150">Visual Studio で、ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューから **[ユーザー シークレットの管理**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1218f-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="1218f-151">このジェスチャは`UserSecretsId`*、.csproj*ファイルに GUID を設定した要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="1218f-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="1218f-152">秘密を設定する</span><span class="sxs-lookup"><span data-stu-id="1218f-152">Set a secret</span></span>

<span data-ttu-id="1218f-153">キーとその値で構成されるアプリ シークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="1218f-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="1218f-154">シークレットはプロジェクトの`UserSecretsId`値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1218f-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="1218f-155">たとえば *、.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="1218f-156">上の例では、コロンはプロパティを`Movies`持つオブジェクト リテラルを`ServiceApiKey`示しています。</span><span class="sxs-lookup"><span data-stu-id="1218f-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="1218f-157">シークレットマネージャツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="1218f-158">`--project` *.csproj*ファイルが存在するファイル システム パスを指定するには、このオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="1218f-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="1218f-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="1218f-160">ビジュアル スタジオでの JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="1218f-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="1218f-161">Visual Studio の**ユーザー シークレットの管理**ジェスチャは、テキスト エディターで*secrets.json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="1218f-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="1218f-162">*secrets.json*の内容を、格納するキーと値のペアに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1218f-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="1218f-163">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1218f-164">JSON 構造は、 または`dotnet user-secrets remove``dotnet user-secrets set`を使用して変更した後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="1218f-165">たとえば、実行すると`dotnet user-secrets remove "Movies:ConnectionString"`オブジェクト リテラル`Movies`が折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="1218f-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="1218f-166">変更されたファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1218f-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="1218f-167">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="1218f-167">Set multiple secrets</span></span>

<span data-ttu-id="1218f-168">シークレットのバッチは、`set`コマンドに JSON をパイプすることで設定できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="1218f-169">次の例では *、input.json*ファイルの内容がコマンドに`set`パイプされます。</span><span class="sxs-lookup"><span data-stu-id="1218f-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1218f-170">Windows</span><span class="sxs-lookup"><span data-stu-id="1218f-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="1218f-171">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="1218f-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="1218f-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1218f-173">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="1218f-174">シークレットにアクセスする</span><span class="sxs-lookup"><span data-stu-id="1218f-174">Access a secret</span></span>

<span data-ttu-id="1218f-175">[ASP.NETコア構成 API は](xref:fundamentals/configuration/index)シークレットマネージャーシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1218f-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="1218f-176">ユーザー シークレット構成ソースは、プロジェクトがホストの新しいインスタンスを<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>初期化するために事前設定された既定値を使用して呼び出すと、開発モードで自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="1218f-177">`CreateDefaultBuilder`の<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName>ときに呼び<xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>出します。</span><span class="sxs-lookup"><span data-stu-id="1218f-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="1218f-178">呼`CreateDefaultBuilder`び出されない場合は、 を呼び出<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>して、ユーザー シークレット構成ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="1218f-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="1218f-179">次`AddUserSecrets`の例に示すように、アプリケーションが開発環境で実行されている場合にのみ呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1218f-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="1218f-180">ユーザーシークレットは`Configuration`API を使用して取得できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="1218f-181">POCO にシークレットをマップする</span><span class="sxs-lookup"><span data-stu-id="1218f-181">Map secrets to a POCO</span></span>

<span data-ttu-id="1218f-182">オブジェクト リテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-183">上記のシークレットを POCO にマップするには、API`Configuration`の[オブジェクト グラフ バインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="1218f-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="1218f-184">次のコードは、カスタム`MovieSettings`POCO にバインドされ、`ServiceApiKey`プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="1218f-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="1218f-185">`Movies:ConnectionString`および`Movies:ServiceApiKey`シークレットは、 のそれぞれのプロパティにマップ`MovieSettings`されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="1218f-186">文字列をシークレットに置き換える</span><span class="sxs-lookup"><span data-stu-id="1218f-186">String replacement with secrets</span></span>

<span data-ttu-id="1218f-187">パスワードをプレーンテキストで保存することは安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="1218f-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="1218f-188">たとえば *、appsettings.json*に格納されているデータベース接続文字列には、指定したユーザーのパスワードが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="1218f-189">より安全な方法は、パスワードを秘密として保存することです。</span><span class="sxs-lookup"><span data-stu-id="1218f-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="1218f-190">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="1218f-191">`Password` *appsettings.json*の接続文字列からキーと値のペアを削除します。</span><span class="sxs-lookup"><span data-stu-id="1218f-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="1218f-192">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="1218f-193">シークレットの値は、接続文字列を<xref:System.Data.SqlClient.SqlConnectionStringBuilder>完了するために、オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="1218f-194">秘密を一覧表示する</span><span class="sxs-lookup"><span data-stu-id="1218f-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-195">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="1218f-196">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="1218f-197">前の例では、キー名のコロンは*secrets.json*内のオブジェクト階層を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="1218f-198">1 つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="1218f-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-199">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="1218f-200">アプリの*secrets.json*ファイルが変更され、キーに関連付けられているキーと値`MoviesConnectionString`のペアが削除されました。</span><span class="sxs-lookup"><span data-stu-id="1218f-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1218f-201">`dotnet user-secrets list`次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="1218f-202">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="1218f-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-203">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="1218f-204">アプリのすべてのユーザー シークレットが*secrets.json*ファイルから削除されました。</span><span class="sxs-lookup"><span data-stu-id="1218f-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="1218f-205">[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="1218f-206">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1218f-206">Additional resources</span></span>

* <span data-ttu-id="1218f-207">IIS からシークレット マネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1218f-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1218f-208">[リック・アンダーソン](https://twitter.com/RickAndMSFT)、[ダニエル・ロス](https://github.com/danroth27)、[スコット・アディ](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="1218f-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1218f-209">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1218f-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1218f-210">このドキュメントでは、開発マシン上の ASP.NET Core アプリの開発中に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1218f-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="1218f-211">パスワードやその他の機密データをソース コードに保存しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="1218f-212">生産の秘密は、開発やテストに使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="1218f-213">シークレットはアプリと共に展開しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="1218f-214">代わりに、シークレットは、環境変数、Azure Key Vault などの制御された手段を使用して運用環境で使用できるようにする必要があります。Azure [Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを保存および保護できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="1218f-215">環境変数</span><span class="sxs-lookup"><span data-stu-id="1218f-215">Environment variables</span></span>

<span data-ttu-id="1218f-216">環境変数は、コードまたはローカル構成ファイルにアプリ シークレットが格納されないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="1218f-217">環境変数は、以前に指定したすべての構成ソースの構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="1218f-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="1218f-218">**個々のユーザー アカウント**のセキュリティが有効になっているASP.NETコア Web アプリを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1218f-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="1218f-219">既定のデータベース接続文字列は、プロジェクトの*appsettings.json*ファイルにキー`DefaultConnection`を付けて含まれています。</span><span class="sxs-lookup"><span data-stu-id="1218f-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="1218f-220">既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB 用です。</span><span class="sxs-lookup"><span data-stu-id="1218f-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="1218f-221">アプリの展開中に`DefaultConnection`、キー値を環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="1218f-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="1218f-222">環境変数には、機密性の高い資格情報を含む完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="1218f-223">環境変数は、通常、暗号化されていないプレーン テキストに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="1218f-224">マシンまたはプロセスが侵害された場合、環境変数は信頼されていないパーティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="1218f-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="1218f-225">ユーザーの秘密の漏えいを防ぐための追加措置が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="1218f-226">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="1218f-226">Secret Manager</span></span>

<span data-ttu-id="1218f-227">シークレット マネージャー ツールは、ASP.NET コア プロジェクトの開発中に機密データを格納します。</span><span class="sxs-lookup"><span data-stu-id="1218f-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="1218f-228">このコンテキストでは、機密データの一部はアプリの秘密です。</span><span class="sxs-lookup"><span data-stu-id="1218f-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="1218f-229">アプリ シークレットは、プロジェクト ツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="1218f-230">アプリ シークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクトで共有されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="1218f-231">アプリシークレットはソース管理にチェックインされません。</span><span class="sxs-lookup"><span data-stu-id="1218f-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="1218f-232">シークレット マネージャー ツールは、保存されているシークレットを暗号化しないので、信頼できるストアとして扱う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1218f-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="1218f-233">開発目的のみのためです。</span><span class="sxs-lookup"><span data-stu-id="1218f-233">It's for development purposes only.</span></span> <span data-ttu-id="1218f-234">キーと値は、ユーザー プロファイル ディレクトリの JSON 設定ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="1218f-235">シークレットマネージャーツールの仕組み</span><span class="sxs-lookup"><span data-stu-id="1218f-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="1218f-236">シークレット マネージャー ツールは、値が格納される場所や方法など、実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="1218f-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="1218f-237">これらの実装の詳細を知らなくても、このツールを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="1218f-238">値は、ローカル コンピューター上のシステムで保護されたユーザー プロファイル フォルダーに JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="1218f-239">Windows</span><span class="sxs-lookup"><span data-stu-id="1218f-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="1218f-240">ファイル システム パス:</span><span class="sxs-lookup"><span data-stu-id="1218f-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="1218f-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="1218f-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1218f-242">ファイル システム パス:</span><span class="sxs-lookup"><span data-stu-id="1218f-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="1218f-243">上記のファイル パスで`<user_secrets_id>`*、.csproj*ファイルで指定された`UserSecretsId`値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1218f-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="1218f-244">シークレット マネージャー ツールで保存されたデータの場所や形式に依存するコードを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="1218f-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="1218f-245">これらの実装の詳細は変更される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-245">These implementation details may change.</span></span> <span data-ttu-id="1218f-246">たとえば、シークレット値は暗号化されませんが、将来の値になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="1218f-247">シークレット ストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="1218f-247">Enable secret storage</span></span>

<span data-ttu-id="1218f-248">シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクト固有の構成設定に対して動作します。</span><span class="sxs-lookup"><span data-stu-id="1218f-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="1218f-249">ユーザー シークレットを使用するには`UserSecretsId`*、.csproj*ファイル内の`PropertyGroup`要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="1218f-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="1218f-250">の内部テキスト`UserSecretsId`は任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="1218f-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="1218f-251">開発者は通常、 の`UserSecretsId`GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="1218f-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="1218f-252">Visual Studio で、ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューから **[ユーザー シークレットの管理**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1218f-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="1218f-253">このジェスチャは`UserSecretsId`*、.csproj*ファイルに GUID を設定した要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="1218f-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="1218f-254">秘密を設定する</span><span class="sxs-lookup"><span data-stu-id="1218f-254">Set a secret</span></span>

<span data-ttu-id="1218f-255">キーとその値で構成されるアプリ シークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="1218f-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="1218f-256">シークレットはプロジェクトの`UserSecretsId`値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1218f-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="1218f-257">たとえば *、.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="1218f-258">上の例では、コロンはプロパティを`Movies`持つオブジェクト リテラルを`ServiceApiKey`示しています。</span><span class="sxs-lookup"><span data-stu-id="1218f-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="1218f-259">シークレットマネージャツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="1218f-260">`--project` *.csproj*ファイルが存在するファイル システム パスを指定するには、このオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="1218f-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="1218f-261">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="1218f-262">ビジュアル スタジオでの JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="1218f-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="1218f-263">Visual Studio の**ユーザー シークレットの管理**ジェスチャは、テキスト エディターで*secrets.json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="1218f-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="1218f-264">*secrets.json*の内容を、格納するキーと値のペアに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1218f-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="1218f-265">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1218f-266">JSON 構造は、 または`dotnet user-secrets remove``dotnet user-secrets set`を使用して変更した後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="1218f-267">たとえば、実行すると`dotnet user-secrets remove "Movies:ConnectionString"`オブジェクト リテラル`Movies`が折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="1218f-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="1218f-268">変更されたファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1218f-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="1218f-269">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="1218f-269">Set multiple secrets</span></span>

<span data-ttu-id="1218f-270">シークレットのバッチは、`set`コマンドに JSON をパイプすることで設定できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="1218f-271">次の例では *、input.json*ファイルの内容がコマンドに`set`パイプされます。</span><span class="sxs-lookup"><span data-stu-id="1218f-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1218f-272">Windows</span><span class="sxs-lookup"><span data-stu-id="1218f-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="1218f-273">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="1218f-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="1218f-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="1218f-275">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="1218f-276">シークレットにアクセスする</span><span class="sxs-lookup"><span data-stu-id="1218f-276">Access a secret</span></span>

<span data-ttu-id="1218f-277">[ASP.NETコア構成 API は](xref:fundamentals/configuration/index)シークレットマネージャーシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1218f-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="1218f-278">プロジェクトが .NET Framework を対象とする場合は、[パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="1218f-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="1218f-279">ASP.NET Core 2.0 以降では、プロジェクトがホストの新しいインスタンスを初期化するために事前設定された<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>既定値を使用して呼び出すと、ユーザー シークレット構成ソースが開発モードで自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="1218f-280">`CreateDefaultBuilder`の<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>ときに呼び<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>出します。</span><span class="sxs-lookup"><span data-stu-id="1218f-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="1218f-281">呼`CreateDefaultBuilder`び出されない場合は、コンストラクターで呼び出<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>して、ユーザー シークレット構成`Startup`ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="1218f-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="1218f-282">次`AddUserSecrets`の例に示すように、アプリケーションが開発環境で実行されている場合にのみ呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1218f-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="1218f-283">ユーザーシークレットは`Configuration`API を使用して取得できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="1218f-284">POCO にシークレットをマップする</span><span class="sxs-lookup"><span data-stu-id="1218f-284">Map secrets to a POCO</span></span>

<span data-ttu-id="1218f-285">オブジェクト リテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-286">上記のシークレットを POCO にマップするには、API`Configuration`の[オブジェクト グラフ バインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="1218f-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="1218f-287">次のコードは、カスタム`MovieSettings`POCO にバインドされ、`ServiceApiKey`プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="1218f-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="1218f-288">`Movies:ConnectionString`および`Movies:ServiceApiKey`シークレットは、 のそれぞれのプロパティにマップ`MovieSettings`されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="1218f-289">文字列をシークレットに置き換える</span><span class="sxs-lookup"><span data-stu-id="1218f-289">String replacement with secrets</span></span>

<span data-ttu-id="1218f-290">パスワードをプレーンテキストで保存することは安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="1218f-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="1218f-291">たとえば *、appsettings.json*に格納されているデータベース接続文字列には、指定したユーザーのパスワードが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1218f-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="1218f-292">より安全な方法は、パスワードを秘密として保存することです。</span><span class="sxs-lookup"><span data-stu-id="1218f-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="1218f-293">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="1218f-294">`Password` *appsettings.json*の接続文字列からキーと値のペアを削除します。</span><span class="sxs-lookup"><span data-stu-id="1218f-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="1218f-295">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="1218f-296">シークレットの値は、接続文字列を<xref:System.Data.SqlClient.SqlConnectionStringBuilder>完了するために、オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定できます。</span><span class="sxs-lookup"><span data-stu-id="1218f-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="1218f-297">秘密を一覧表示する</span><span class="sxs-lookup"><span data-stu-id="1218f-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-298">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="1218f-299">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="1218f-300">前の例では、キー名のコロンは*secrets.json*内のオブジェクト階層を示します。</span><span class="sxs-lookup"><span data-stu-id="1218f-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="1218f-301">1 つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="1218f-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-302">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="1218f-303">アプリの*secrets.json*ファイルが変更され、キーに関連付けられているキーと値`MoviesConnectionString`のペアが削除されました。</span><span class="sxs-lookup"><span data-stu-id="1218f-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="1218f-304">[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="1218f-305">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="1218f-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="1218f-306">*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1218f-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="1218f-307">アプリのすべてのユーザー シークレットが*secrets.json*ファイルから削除されました。</span><span class="sxs-lookup"><span data-stu-id="1218f-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="1218f-308">[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1218f-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="1218f-309">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1218f-309">Additional resources</span></span>

* <span data-ttu-id="1218f-310">IIS からシークレット マネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1218f-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end