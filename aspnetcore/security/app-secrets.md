---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776683"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="89bfb-103">ASP.NET Core での開発におけるアプリシークレットの安全な保存</span><span class="sxs-lookup"><span data-stu-id="89bfb-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89bfb-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="89bfb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="89bfb-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="89bfb-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="89bfb-106">このドキュメントでは、開発用コンピューターでの ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="89bfb-107">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="89bfb-108">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="89bfb-109">シークレットはアプリと一緒にデプロイしないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="89bfb-110">代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="89bfb-111">環境変数</span><span class="sxs-lookup"><span data-stu-id="89bfb-111">Environment variables</span></span>

<span data-ttu-id="89bfb-112">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="89bfb-113">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="89bfb-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="89bfb-114">**個々のユーザーアカウント**のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="89bfb-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="89bfb-115">既定のデータベース接続文字列は、というキー `DefaultConnection`を持つプロジェクトの*appsettings*ファイルに含まれています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="89bfb-116">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="89bfb-117">アプリの展開時に`DefaultConnection` 、キー値は環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="89bfb-118">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="89bfb-119">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="89bfb-120">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="89bfb-121">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="89bfb-122">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="89bfb-122">Secret Manager</span></span>

<span data-ttu-id="89bfb-123">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="89bfb-124">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="89bfb-125">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="89bfb-126">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="89bfb-127">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="89bfb-128">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="89bfb-129">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-129">It's for development purposes only.</span></span> <span data-ttu-id="89bfb-130">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="89bfb-131">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="89bfb-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="89bfb-132">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="89bfb-133">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="89bfb-134">値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="89bfb-135">Windows</span><span class="sxs-lookup"><span data-stu-id="89bfb-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="89bfb-136">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="89bfb-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="89bfb-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="89bfb-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="89bfb-138">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="89bfb-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="89bfb-139">上記のファイルパスで、を`<user_secrets_id>` .csproj ファイル`UserSecretsId`で指定され *.csproj*た値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="89bfb-140">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="89bfb-141">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-141">These implementation details may change.</span></span> <span data-ttu-id="89bfb-142">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="89bfb-143">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="89bfb-143">Enable secret storage</span></span>

<span data-ttu-id="89bfb-144">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="89bfb-145">Secret Manager ツールには .NET Core SDK `init` 3.0.100 以降のコマンドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="89bfb-146">ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="89bfb-147">上のコマンドは、 `UserSecretsId` .csproj ファイルの`PropertyGroup`内に *.csproj*要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="89bfb-148">既定では、の`UserSecretsId`内部テキストは GUID です。</span><span class="sxs-lookup"><span data-stu-id="89bfb-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="89bfb-149">内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="89bfb-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="89bfb-150">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [**ユーザーシークレットの管理**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="89bfb-151">このジェスチャは、 `UserSecretsId` GUID が設定された要素を .csproj ファイルに追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="89bfb-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="89bfb-152">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="89bfb-152">Set a secret</span></span>

<span data-ttu-id="89bfb-153">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="89bfb-154">シークレットは、プロジェクトの`UserSecretsId`値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="89bfb-155">たとえば、 *.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="89bfb-156">前の例では、コロンは、 `Movies`が`ServiceApiKey`プロパティを持つオブジェクトリテラルであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="89bfb-157">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="89bfb-158">.Csproj ファイル`--project`が存在するファイルシステムパスを指定するに *.csproj*は、オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="89bfb-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="89bfb-160">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="89bfb-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="89bfb-161">Visual Studio の [**ユーザーシークレットの管理**] ジェスチャは、テキストエディターで*シークレットの json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="89bfb-162">*シークレット*の内容を、格納されるキーと値のペアで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="89bfb-163">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="89bfb-164">JSON 構造体は、または`dotnet user-secrets remove` `dotnet user-secrets set`を使用した変更後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="89bfb-165">たとえば、を実行`dotnet user-secrets remove "Movies:ConnectionString"`すると`Movies` 、オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="89bfb-166">変更後のファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="89bfb-167">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="89bfb-167">Set multiple secrets</span></span>

<span data-ttu-id="89bfb-168">シークレットのバッチは、JSON を`set`コマンドにパイプすることによって設定できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="89bfb-169">次の例では、*入力した json*ファイルの内容を`set`コマンドにパイプ処理します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="89bfb-170">Windows</span><span class="sxs-lookup"><span data-stu-id="89bfb-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="89bfb-171">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="89bfb-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="89bfb-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="89bfb-173">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="89bfb-174">シークレットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="89bfb-174">Access a secret</span></span>

<span data-ttu-id="89bfb-175">[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="89bfb-176">ユーザーシークレットの構成ソースは、プロジェクトがを呼び出し<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>て、構成済みの既定値でホストの新しいインスタンスを初期化するときに、自動的に開発モードで追加されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="89bfb-177">`CreateDefaultBuilder`が<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>の場合<xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 、 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="89bfb-178">が`CreateDefaultBuilder`呼び出されない場合は、を呼び出し<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>てユーザーシークレットの構成ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="89bfb-179">次`AddUserSecrets`の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="89bfb-180">ユーザーシークレットは API を使用し`Configuration`て取得できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="89bfb-181">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="89bfb-181">Map secrets to a POCO</span></span>

<span data-ttu-id="89bfb-182">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-183">前のシークレットを POCO にマップするには、 `Configuration` API の[オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="89bfb-184">次のコードは、カスタム`MovieSettings` POCO にバインドし、 `ServiceApiKey`プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="89bfb-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="89bfb-185">と`Movies:ConnectionString` `Movies:ServiceApiKey`シークレットは、の`MovieSettings`それぞれのプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="89bfb-186">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="89bfb-186">String replacement with secrets</span></span>

<span data-ttu-id="89bfb-187">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="89bfb-188">たとえば、 *appsettings*に格納されているデータベース接続文字列には、指定されたユーザーのパスワードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="89bfb-189">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="89bfb-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="89bfb-190">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="89bfb-191">Appsettings の`Password`接続文字列からキーと値のペアを*appsettings.json*削除します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="89bfb-192">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="89bfb-193">シークレットの値は、 <xref:System.Data.SqlClient.SqlConnectionStringBuilder>オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定して、接続文字列を完成させることができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="89bfb-194">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="89bfb-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-195">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="89bfb-196">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="89bfb-197">前の例では、キー名のコロンは、 *json*内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="89bfb-198">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="89bfb-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-199">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="89bfb-200">アプリの*シークレットの json*ファイルは、 `MoviesConnectionString`キーに関連付けられているキーと値のペアを削除するように変更されました。</span><span class="sxs-lookup"><span data-stu-id="89bfb-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="89bfb-201">`dotnet user-secrets list`次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="89bfb-202">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="89bfb-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-203">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="89bfb-204">アプリのすべてのユーザーシークレットが、*シークレットの json*ファイルから削除されています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="89bfb-205">を`dotnet user-secrets list`実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="89bfb-206">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="89bfb-206">Additional resources</span></span>

* <span data-ttu-id="89bfb-207">IIS からシークレットマネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89bfb-208">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="89bfb-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="89bfb-209">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="89bfb-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="89bfb-210">このドキュメントでは、開発用コンピューターでの ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="89bfb-211">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="89bfb-212">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="89bfb-213">シークレットはアプリと一緒にデプロイしないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="89bfb-214">代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="89bfb-215">環境変数</span><span class="sxs-lookup"><span data-stu-id="89bfb-215">Environment variables</span></span>

<span data-ttu-id="89bfb-216">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="89bfb-217">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="89bfb-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="89bfb-218">**個々のユーザーアカウント**のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="89bfb-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="89bfb-219">既定のデータベース接続文字列は、というキー `DefaultConnection`を持つプロジェクトの*appsettings*ファイルに含まれています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="89bfb-220">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="89bfb-221">アプリの展開時に`DefaultConnection` 、キー値は環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="89bfb-222">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="89bfb-223">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="89bfb-224">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="89bfb-225">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="89bfb-226">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="89bfb-226">Secret Manager</span></span>

<span data-ttu-id="89bfb-227">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="89bfb-228">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="89bfb-229">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="89bfb-230">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="89bfb-231">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="89bfb-232">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="89bfb-233">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-233">It's for development purposes only.</span></span> <span data-ttu-id="89bfb-234">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="89bfb-235">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="89bfb-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="89bfb-236">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="89bfb-237">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="89bfb-238">値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="89bfb-239">Windows</span><span class="sxs-lookup"><span data-stu-id="89bfb-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="89bfb-240">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="89bfb-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="89bfb-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="89bfb-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="89bfb-242">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="89bfb-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="89bfb-243">上記のファイルパスで、を`<user_secrets_id>` .csproj ファイル`UserSecretsId`で指定され *.csproj*た値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="89bfb-244">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="89bfb-245">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-245">These implementation details may change.</span></span> <span data-ttu-id="89bfb-246">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="89bfb-247">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="89bfb-247">Enable secret storage</span></span>

<span data-ttu-id="89bfb-248">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="89bfb-249">ユーザーシークレットを使用するには`UserSecretsId` 、.csproj ファイル`PropertyGroup`の内 *.csproj*に要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="89bfb-250">の`UserSecretsId`内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="89bfb-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="89bfb-251">通常、 `UserSecretsId`開発者はの GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="89bfb-252">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [**ユーザーシークレットの管理**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="89bfb-253">このジェスチャは、 `UserSecretsId` GUID が設定された要素を .csproj ファイルに追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="89bfb-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="89bfb-254">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="89bfb-254">Set a secret</span></span>

<span data-ttu-id="89bfb-255">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="89bfb-256">シークレットは、プロジェクトの`UserSecretsId`値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="89bfb-257">たとえば、 *.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="89bfb-258">前の例では、コロンは、 `Movies`が`ServiceApiKey`プロパティを持つオブジェクトリテラルであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="89bfb-259">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="89bfb-260">.Csproj ファイル`--project`が存在するファイルシステムパスを指定するに *.csproj*は、オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="89bfb-261">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="89bfb-262">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="89bfb-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="89bfb-263">Visual Studio の [**ユーザーシークレットの管理**] ジェスチャは、テキストエディターで*シークレットの json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="89bfb-264">*シークレット*の内容を、格納されるキーと値のペアで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="89bfb-265">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="89bfb-266">JSON 構造体は、または`dotnet user-secrets remove` `dotnet user-secrets set`を使用した変更後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="89bfb-267">たとえば、を実行`dotnet user-secrets remove "Movies:ConnectionString"`すると`Movies` 、オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="89bfb-268">変更後のファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="89bfb-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="89bfb-269">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="89bfb-269">Set multiple secrets</span></span>

<span data-ttu-id="89bfb-270">シークレットのバッチは、JSON を`set`コマンドにパイプすることによって設定できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="89bfb-271">次の例では、*入力した json*ファイルの内容を`set`コマンドにパイプ処理します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="89bfb-272">Windows</span><span class="sxs-lookup"><span data-stu-id="89bfb-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="89bfb-273">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="89bfb-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="89bfb-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="89bfb-275">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="89bfb-276">シークレットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="89bfb-276">Access a secret</span></span>

<span data-ttu-id="89bfb-277">[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="89bfb-278">プロジェクトが .NET Framework 対象である場合は、 [Microsoft. Extensions. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="89bfb-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="89bfb-279">ASP.NET Core 2.0 以降では、プロジェクトがを呼び出し<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>て、事前に構成された既定値でホストの新しいインスタンスを初期化すると、開発モードでユーザーシークレットの構成ソースが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="89bfb-280">`CreateDefaultBuilder`が<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>の場合<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 、 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="89bfb-281">が`CreateDefaultBuilder`呼び出されない場合は、 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup`コンストラクターでを呼び出すことによって、ユーザーシークレット構成ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="89bfb-282">次`AddUserSecrets`の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="89bfb-283">ユーザーシークレットは API を使用し`Configuration`て取得できます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="89bfb-284">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="89bfb-284">Map secrets to a POCO</span></span>

<span data-ttu-id="89bfb-285">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-286">前のシークレットを POCO にマップするには、 `Configuration` API の[オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="89bfb-287">次のコードは、カスタム`MovieSettings` POCO にバインドし、 `ServiceApiKey`プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="89bfb-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="89bfb-288">と`Movies:ConnectionString` `Movies:ServiceApiKey`シークレットは、の`MovieSettings`それぞれのプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="89bfb-289">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="89bfb-289">String replacement with secrets</span></span>

<span data-ttu-id="89bfb-290">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="89bfb-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="89bfb-291">たとえば、 *appsettings*に格納されているデータベース接続文字列には、指定されたユーザーのパスワードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="89bfb-292">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="89bfb-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="89bfb-293">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="89bfb-294">Appsettings の`Password`接続文字列からキーと値のペアを*appsettings.json*削除します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="89bfb-295">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="89bfb-296">シークレットの値は、 <xref:System.Data.SqlClient.SqlConnectionStringBuilder>オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定して、接続文字列を完成させることができます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="89bfb-297">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="89bfb-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-298">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="89bfb-299">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="89bfb-300">前の例では、キー名のコロンは、 *json*内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="89bfb-301">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="89bfb-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-302">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="89bfb-303">アプリの*シークレットの json*ファイルは、 `MoviesConnectionString`キーに関連付けられているキーと値のペアを削除するように変更されました。</span><span class="sxs-lookup"><span data-stu-id="89bfb-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="89bfb-304">を`dotnet user-secrets list`実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="89bfb-305">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="89bfb-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="89bfb-306">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89bfb-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="89bfb-307">アプリのすべてのユーザーシークレットが、*シークレットの json*ファイルから削除されています。</span><span class="sxs-lookup"><span data-stu-id="89bfb-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="89bfb-308">を`dotnet user-secrets list`実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89bfb-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="89bfb-309">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="89bfb-309">Additional resources</span></span>

* <span data-ttu-id="89bfb-310">IIS からシークレットマネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89bfb-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end