---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に機密情報を格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486201"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="6855b-103">ASP.NET Core での開発におけるアプリシークレットの安全な保存</span><span class="sxs-lookup"><span data-stu-id="6855b-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6855b-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6855b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6855b-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6855b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6855b-106">このドキュメントでは、開発用コンピューターで ASP.NET Core アプリの機密データを管理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6855b-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="6855b-107">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="6855b-108">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="6855b-109">シークレットはアプリと一緒にデプロイしないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="6855b-110">代わりに、運用環境のシークレットには、環境変数や Azure Key Vault などの制御された手段を使用してアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="6855b-111">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。</span><span class="sxs-lookup"><span data-stu-id="6855b-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="6855b-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="6855b-112">Environment variables</span></span>

<span data-ttu-id="6855b-113">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="6855b-114">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="6855b-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="6855b-115">**個々のユーザーアカウント** のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6855b-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="6855b-116">既定のデータベース接続文字列は、プロジェクトのファイル内の *appsettings.json* キーと共に含まれ `DefaultConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="6855b-117">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6855b-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="6855b-118">アプリの展開時に、 `DefaultConnection` キー値は環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="6855b-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="6855b-119">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="6855b-120">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="6855b-121">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6855b-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="6855b-122">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="6855b-123">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="6855b-123">Secret Manager</span></span>

<span data-ttu-id="6855b-124">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="6855b-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="6855b-125">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="6855b-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="6855b-126">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="6855b-127">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="6855b-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="6855b-128">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="6855b-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="6855b-129">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="6855b-130">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-130">It's for development purposes only.</span></span> <span data-ttu-id="6855b-131">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="6855b-132">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="6855b-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="6855b-133">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を非表示にします。</span><span class="sxs-lookup"><span data-stu-id="6855b-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="6855b-134">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="6855b-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="6855b-135">値は、ローカルコンピューターのユーザープロファイルフォルダー内の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="6855b-136">Windows</span><span class="sxs-lookup"><span data-stu-id="6855b-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="6855b-137">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="6855b-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="6855b-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="6855b-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6855b-139">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="6855b-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="6855b-140">上記のファイルパスで、を `<user_secrets_id>` `UserSecretsId` プロジェクトファイルで指定された値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6855b-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="6855b-141">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="6855b-142">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-142">These implementation details may change.</span></span> <span data-ttu-id="6855b-143">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="6855b-144">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="6855b-144">Enable secret storage</span></span>

<span data-ttu-id="6855b-145">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="6855b-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="6855b-146">Secret Manager ツールには .NET Core SDK 3.0.100 以降のコマンドが含まれてい `init` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="6855b-147">ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="6855b-148">上記のコマンドは、 `UserSecretsId` プロジェクトファイルの内に要素を追加し `PropertyGroup` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="6855b-149">既定では、の内部テキスト `UserSecretsId` は GUID です。</span><span class="sxs-lookup"><span data-stu-id="6855b-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="6855b-150">内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="6855b-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="6855b-151">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [ **ユーザーシークレットの管理** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6855b-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6855b-152">このジェスチャは、GUID が設定された `UserSecretsId` 要素をプロジェクトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6855b-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="6855b-153">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="6855b-153">Set a secret</span></span>

<span data-ttu-id="6855b-154">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="6855b-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="6855b-155">シークレットは、プロジェクトの値に関連付けられ `UserSecretsId` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="6855b-156">たとえば、プロジェクトファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="6855b-157">前の例では、コロンは、がプロパティを持つオブジェクトリテラルであることを示して `Movies` い `ServiceApiKey` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="6855b-158">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="6855b-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="6855b-159">オプションを使用して、 `--project` プロジェクトファイルが存在するファイルシステムパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6855b-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="6855b-160">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="6855b-161">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="6855b-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="6855b-162">Visual Studio の [ **ユーザーシークレットの管理** ] ジェスチャでは、テキストエディターでファイル *のsecrets.js* が開きます。</span><span class="sxs-lookup"><span data-stu-id="6855b-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="6855b-163">*secrets.js* の内容を、格納されるキーと値のペアに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6855b-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="6855b-164">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6855b-165">JSON 構造体は、またはを使用した変更後にフラット化され `dotnet user-secrets remove` `dotnet user-secrets set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="6855b-166">たとえば、を実行 `dotnet user-secrets remove "Movies:ConnectionString"` すると、 `Movies` オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="6855b-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="6855b-167">変更されたファイルは、次の JSON のようになります。</span><span class="sxs-lookup"><span data-stu-id="6855b-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="6855b-168">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="6855b-168">Set multiple secrets</span></span>

<span data-ttu-id="6855b-169">シークレットのバッチは、JSON をコマンドにパイプすることによって設定でき `set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="6855b-170">次の例では、ファイルの内容の *input.js* がコマンドにパイプされてい `set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="6855b-171">Windows</span><span class="sxs-lookup"><span data-stu-id="6855b-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="6855b-172">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="6855b-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="6855b-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6855b-174">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="6855b-175">シークレットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="6855b-175">Access a secret</span></span>

<span data-ttu-id="6855b-176">シークレットにアクセスするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="6855b-177">ユーザーシークレットの構成ソースを登録する</span><span class="sxs-lookup"><span data-stu-id="6855b-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="6855b-178">構成 API を使用してシークレットを読み取る</span><span class="sxs-lookup"><span data-stu-id="6855b-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="6855b-179">ユーザーシークレットの構成ソースを登録する</span><span class="sxs-lookup"><span data-stu-id="6855b-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="6855b-180">ユーザーシークレット [構成プロバイダー](/dotnet/core/extensions/configuration-providers) は、適切な構成ソースを .NET [構成 API](xref:fundamentals/configuration/index)に登録します。</span><span class="sxs-lookup"><span data-stu-id="6855b-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="6855b-181">ユーザーシークレットの構成ソースは、プロジェクトがを呼び出すときに、開発モードで自動的に追加され <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="6855b-182">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>がの場合、 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> を呼び出し <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="6855b-183">`CreateDefaultBuilder`が呼び出されない場合は、でを呼び出してユーザーシークレットの構成ソースを明示的に追加し <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="6855b-184">`AddUserSecrets`次の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6855b-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="6855b-185">構成 API を使用してシークレットを読み取る</span><span class="sxs-lookup"><span data-stu-id="6855b-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="6855b-186">ユーザーシークレットの構成ソースが登録されている場合、.NET 構成 API はシークレットを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="6855b-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="6855b-187">[コンストラクターインジェクション](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) を使用して、.NET 構成 API にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6855b-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="6855b-188">次のキーの読み取り例を考えてみましょう `Movies:ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="6855b-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="6855b-189">**スタートアップクラス:**</span><span class="sxs-lookup"><span data-stu-id="6855b-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="6855b-190">**Razor ページページモデル:**</span><span class="sxs-lookup"><span data-stu-id="6855b-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="6855b-191">詳細については、ページの「スタートアップと[アクセスの Razor 構成](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)の[アクセス](xref:fundamentals/configuration/index#access-configuration-in-startup)構成」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6855b-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="6855b-192">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="6855b-192">Map secrets to a POCO</span></span>

<span data-ttu-id="6855b-193">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6855b-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-194">上記のシークレットを POCO にマップするには、.NET 構成 API の [オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph) 機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="6855b-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="6855b-195">次のコードは、カスタム POCO にバインド `MovieSettings` し、 `ServiceApiKey` プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6855b-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="6855b-196">`Movies:ConnectionString`と `Movies:ServiceApiKey` シークレットは、のそれぞれのプロパティにマップされ `MovieSettings` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="6855b-197">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="6855b-197">String replacement with secrets</span></span>

<span data-ttu-id="6855b-198">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="6855b-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="6855b-199">たとえば、に格納されているデータベース接続文字列には、 *appsettings.json* 指定されたユーザーのパスワードが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="6855b-200">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="6855b-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="6855b-201">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="6855b-202">`Password`の接続文字列からキーと値のペアを削除 *appsettings.json* します。</span><span class="sxs-lookup"><span data-stu-id="6855b-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="6855b-203">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="6855b-204">シークレットの値は、オブジェクトのプロパティに設定して、接続文字列を完成させることができ <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="6855b-205">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="6855b-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-206">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="6855b-207">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="6855b-208">前の例では、キー名のコロンは *secrets.js* 内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="6855b-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="6855b-209">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="6855b-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-210">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="6855b-211">ファイルのアプリの *secrets.js* は、キーに関連付けられているキーと値のペアを削除するように変更されました `MoviesConnectionString` 。</span><span class="sxs-lookup"><span data-stu-id="6855b-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6855b-212">`dotnet user-secrets list` 次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="6855b-213">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="6855b-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-214">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="6855b-215">アプリのすべてのユーザーシークレットは、ファイルの *secrets.js* から削除されています。</span><span class="sxs-lookup"><span data-stu-id="6855b-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="6855b-216">`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="6855b-217">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6855b-217">Additional resources</span></span>

* <span data-ttu-id="6855b-218">IIS からのユーザーシークレットへのアクセスの詳細については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6855b-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6855b-219">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6855b-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6855b-220">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6855b-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6855b-221">このドキュメントでは、開発用コンピューターで ASP.NET Core アプリの機密データを管理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6855b-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="6855b-222">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="6855b-223">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="6855b-224">シークレットはアプリと一緒にデプロイしないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="6855b-225">代わりに、運用環境のシークレットには、環境変数や Azure Key Vault などの制御された手段を使用してアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="6855b-226">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。</span><span class="sxs-lookup"><span data-stu-id="6855b-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="6855b-227">環境変数</span><span class="sxs-lookup"><span data-stu-id="6855b-227">Environment variables</span></span>

<span data-ttu-id="6855b-228">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="6855b-229">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="6855b-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="6855b-230">**個々のユーザーアカウント** のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6855b-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="6855b-231">既定のデータベース接続文字列は、プロジェクトのファイル内の *appsettings.json* キーと共に含まれ `DefaultConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="6855b-232">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6855b-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="6855b-233">アプリの展開時に、 `DefaultConnection` キー値は環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="6855b-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="6855b-234">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="6855b-235">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="6855b-236">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6855b-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="6855b-237">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="6855b-238">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="6855b-238">Secret Manager</span></span>

<span data-ttu-id="6855b-239">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="6855b-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="6855b-240">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="6855b-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="6855b-241">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="6855b-242">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="6855b-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="6855b-243">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="6855b-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="6855b-244">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="6855b-245">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-245">It's for development purposes only.</span></span> <span data-ttu-id="6855b-246">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="6855b-247">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="6855b-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="6855b-248">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を非表示にします。</span><span class="sxs-lookup"><span data-stu-id="6855b-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="6855b-249">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="6855b-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="6855b-250">値は、ローカルコンピューターのユーザープロファイルフォルダー内の JSON ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="6855b-251">Windows</span><span class="sxs-lookup"><span data-stu-id="6855b-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="6855b-252">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="6855b-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="6855b-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="6855b-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6855b-254">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="6855b-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="6855b-255">上記のファイルパスで、を `<user_secrets_id>` `UserSecretsId` プロジェクトファイルで指定された値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6855b-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="6855b-256">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="6855b-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="6855b-257">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-257">These implementation details may change.</span></span> <span data-ttu-id="6855b-258">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="6855b-259">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="6855b-259">Enable secret storage</span></span>

<span data-ttu-id="6855b-260">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="6855b-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="6855b-261">ユーザーシークレットを使用するには、 `UserSecretsId` プロジェクトファイルの内に要素を定義し `PropertyGroup` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="6855b-262">の内部テキスト `UserSecretsId` は任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="6855b-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="6855b-263">通常、開発者はの GUID を生成 `UserSecretsId` します。</span><span class="sxs-lookup"><span data-stu-id="6855b-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="6855b-264">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから [ **ユーザーシークレットの管理** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="6855b-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6855b-265">このジェスチャは、GUID が設定された `UserSecretsId` 要素をプロジェクトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6855b-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="6855b-266">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="6855b-266">Set a secret</span></span>

<span data-ttu-id="6855b-267">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="6855b-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="6855b-268">シークレットは、プロジェクトの値に関連付けられ `UserSecretsId` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="6855b-269">たとえば、プロジェクトファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="6855b-270">前の例では、コロンは、がプロパティを持つオブジェクトリテラルであることを示して `Movies` い `ServiceApiKey` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="6855b-271">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="6855b-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="6855b-272">オプションを使用して、 `--project` プロジェクトファイルが存在するファイルシステムパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6855b-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="6855b-273">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="6855b-274">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="6855b-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="6855b-275">Visual Studio の [ **ユーザーシークレットの管理** ] ジェスチャでは、テキストエディターでファイル *のsecrets.js* が開きます。</span><span class="sxs-lookup"><span data-stu-id="6855b-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="6855b-276">*secrets.js* の内容を、格納されるキーと値のペアに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6855b-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="6855b-277">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6855b-278">JSON 構造体は、またはを使用した変更後にフラット化され `dotnet user-secrets remove` `dotnet user-secrets set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="6855b-279">たとえば、を実行 `dotnet user-secrets remove "Movies:ConnectionString"` すると、 `Movies` オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="6855b-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="6855b-280">変更されたファイルは、次の JSON のようになります。</span><span class="sxs-lookup"><span data-stu-id="6855b-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="6855b-281">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="6855b-281">Set multiple secrets</span></span>

<span data-ttu-id="6855b-282">シークレットのバッチは、JSON をコマンドにパイプすることによって設定でき `set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="6855b-283">次の例では、ファイルの内容の *input.js* がコマンドにパイプされてい `set` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="6855b-284">Windows</span><span class="sxs-lookup"><span data-stu-id="6855b-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="6855b-285">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="6855b-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="6855b-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6855b-287">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="6855b-288">シークレットへのアクセス</span><span class="sxs-lookup"><span data-stu-id="6855b-288">Access a secret</span></span>

<span data-ttu-id="6855b-289">[構成 API](xref:fundamentals/configuration/index)は、ユーザーシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6855b-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="6855b-290">プロジェクトが .NET Framework 対象である場合は、Microsoft.Extensions.Configuration をインストールし [ ます。UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="6855b-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="6855b-291">ASP.NET Core 2.0 以降では、プロジェクトがを呼び出すときに、ユーザーシークレットの構成ソースが開発モードで自動的に追加され <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="6855b-292">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>がの場合、 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> を呼び出し <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="6855b-293">`CreateDefaultBuilder`が呼び出されない場合は、コンストラクターでを呼び出すことによって、ユーザーシークレット構成ソースを明示的に追加し <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="6855b-294">`AddUserSecrets`次の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6855b-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="6855b-295">ユーザーシークレットは、.NET 構成 API を使用して取得できます。</span><span class="sxs-lookup"><span data-stu-id="6855b-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="6855b-296">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="6855b-296">Map secrets to a POCO</span></span>

<span data-ttu-id="6855b-297">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6855b-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-298">上記のシークレットを POCO にマップするには、.NET 構成 API の [オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph) 機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="6855b-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="6855b-299">次のコードは、カスタム POCO にバインド `MovieSettings` し、 `ServiceApiKey` プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6855b-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="6855b-300">`Movies:ConnectionString`と `Movies:ServiceApiKey` シークレットは、のそれぞれのプロパティにマップされ `MovieSettings` ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="6855b-301">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="6855b-301">String replacement with secrets</span></span>

<span data-ttu-id="6855b-302">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="6855b-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="6855b-303">たとえば、に格納されているデータベース接続文字列には、 *appsettings.json* 指定されたユーザーのパスワードが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6855b-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="6855b-304">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="6855b-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="6855b-305">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="6855b-306">`Password`の接続文字列からキーと値のペアを削除 *appsettings.json* します。</span><span class="sxs-lookup"><span data-stu-id="6855b-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="6855b-307">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6855b-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="6855b-308">シークレットの値は、オブジェクトのプロパティに設定して、接続文字列を完成させることができ <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="6855b-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="6855b-309">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="6855b-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-310">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="6855b-311">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="6855b-312">前の例では、キー名のコロンは *secrets.js* 内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="6855b-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="6855b-313">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="6855b-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-314">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="6855b-315">ファイルのアプリの *secrets.js* は、キーに関連付けられているキーと値のペアを削除するように変更されました `MoviesConnectionString` 。</span><span class="sxs-lookup"><span data-stu-id="6855b-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6855b-316">`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="6855b-317">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="6855b-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6855b-318">プロジェクトファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6855b-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="6855b-319">アプリのすべてのユーザーシークレットは、ファイルの *secrets.js* から削除されています。</span><span class="sxs-lookup"><span data-stu-id="6855b-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="6855b-320">`dotnet user-secrets list`を実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6855b-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="6855b-321">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6855b-321">Additional resources</span></span>

* <span data-ttu-id="6855b-322">IIS からのユーザーシークレットへのアクセスの詳細については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6855b-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end