---
<span data-ttu-id="8b81e-101">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-102">'Blazor'</span></span>
- <span data-ttu-id="8b81e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-103">'Identity'</span></span>
- <span data-ttu-id="8b81e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-105">'Razor'</span></span>
- <span data-ttu-id="8b81e-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="8b81e-107">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b81e-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="8b81e-108">By [Andrew Stanton-看護師](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8b81e-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b81e-109">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8b81e-110">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8b81e-111">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8b81e-112">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="8b81e-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8b81e-113">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="8b81e-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8b81e-114">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8b81e-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8b81e-115">パッケージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-115">Packages</span></span>

<span data-ttu-id="8b81e-116">パッケージ[への参照を、パッケージへ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8b81e-117">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="8b81e-117">Sample app</span></span>

<span data-ttu-id="8b81e-118">サンプルアプリは、 `#define` *Program.cs*ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8b81e-119">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8b81e-120">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8b81e-121">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8b81e-122">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8b81e-123">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8b81e-124">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8b81e-125">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8b81e-126">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="8b81e-127">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8b81e-128">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8b81e-129">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8b81e-130">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8b81e-131">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8b81e-132">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8b81e-133">シークレットマネージャーは、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8b81e-134">プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8b81e-135">これらのシークレットが Azure Key Vault セクションを[含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8b81e-136">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8b81e-137">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8b81e-138">クイックスタートに記載されている手順[: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli)する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8b81e-139">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8b81e-140">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8b81e-141">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8b81e-142">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8b81e-143">[ **Cloud Shell の起動**] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8b81e-144">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8b81e-145">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8b81e-146">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8b81e-147">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8b81e-148">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8b81e-149">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8b81e-150">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8b81e-151">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8b81e-152">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8b81e-153">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8b81e-154">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8b81e-155">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8b81e-156">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8b81e-157">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8b81e-158">**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8b81e-159">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8b81e-160">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8b81e-161">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8b81e-162">このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8b81e-163">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8b81e-164">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8b81e-165">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8b81e-166">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8b81e-167">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8b81e-168">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8b81e-169">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8b81e-170">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8b81e-171">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8b81e-172">[**証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8b81e-173">公開キーを含む証明書をアップロードするには、[**証明書のアップロード**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8b81e-174">*.Cer*、 *pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8b81e-175">Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8b81e-176">Azure portal の**キーコンテナー**に移動します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8b81e-177">Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8b81e-178">[**アクセスポリシー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="8b81e-179">[**アクセスポリシーの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8b81e-180">[**シークレットのアクセス許可**] を開き、アプリに**Get**および**List**アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8b81e-181">[**プリンシパルの選択**] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8b81e-182">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="8b81e-183">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-183">Select **OK**.</span></span>
1. <span data-ttu-id="8b81e-184">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-184">Select **Save**.</span></span>
1. <span data-ttu-id="8b81e-185">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-185">Deploy the app.</span></span>

<span data-ttu-id="8b81e-186">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8b81e-187">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8b81e-188">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8b81e-189">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8b81e-190">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8b81e-191">アプリは、 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings*ファイルによって指定された値を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8b81e-192">値の例:</span><span class="sxs-lookup"><span data-stu-id="8b81e-192">Example values:</span></span>

* <span data-ttu-id="8b81e-193">Key vault 名:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8b81e-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8b81e-194">アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8b81e-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8b81e-195">証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8b81e-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8b81e-196">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b81e-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8b81e-197">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8b81e-198">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8b81e-199">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8b81e-200">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8b81e-201">**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8b81e-202">このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8b81e-203">アプリの*appsettings*ファイルにコンテナー名を入力します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8b81e-204">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8b81e-205">アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8b81e-206">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8b81e-207">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8b81e-208">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8b81e-209">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8b81e-210">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8b81e-211">Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8b81e-212">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="8b81e-212">The sample app:</span></span>

* <span data-ttu-id="8b81e-213">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8b81e-214">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8b81e-215"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8b81e-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8b81e-217">Key vault 名の値の例:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8b81e-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8b81e-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b81e-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8b81e-219">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8b81e-220">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8b81e-221">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8b81e-222">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8b81e-223">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8b81e-224">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「[管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="8b81e-225">構成オプション</span><span class="sxs-lookup"><span data-stu-id="8b81e-225">Configuration options</span></span>

<span data-ttu-id="8b81e-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>次のものを受け入れることができ <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="8b81e-227">プロパティ</span><span class="sxs-lookup"><span data-stu-id="8b81e-227">Property</span></span>         | <span data-ttu-id="8b81e-228">説明</span><span class="sxs-lookup"><span data-stu-id="8b81e-228">Description</span></span> |
| ---
<span data-ttu-id="8b81e-229">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-230">'Blazor'</span></span>
- <span data-ttu-id="8b81e-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-231">'Identity'</span></span>
- <span data-ttu-id="8b81e-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-233">'Razor'</span></span>
- <span data-ttu-id="8b81e-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-235">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-236">'Blazor'</span></span>
- <span data-ttu-id="8b81e-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-237">'Identity'</span></span>
- <span data-ttu-id="8b81e-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-239">'Razor'</span></span>
- <span data-ttu-id="8b81e-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-241">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-242">'Blazor'</span></span>
- <span data-ttu-id="8b81e-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-243">'Identity'</span></span>
- <span data-ttu-id="8b81e-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-245">'Razor'</span></span>
- <span data-ttu-id="8b81e-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-247">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-248">'Blazor'</span></span>
- <span data-ttu-id="8b81e-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-249">'Identity'</span></span>
- <span data-ttu-id="8b81e-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-251">'Razor'</span></span>
- <span data-ttu-id="8b81e-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-253">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-254">'Blazor'</span></span>
- <span data-ttu-id="8b81e-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-255">'Identity'</span></span>
- <span data-ttu-id="8b81e-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-257">'Razor'</span></span>
- <span data-ttu-id="8b81e-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-259">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-260">'Blazor'</span></span>
- <span data-ttu-id="8b81e-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-261">'Identity'</span></span>
- <span data-ttu-id="8b81e-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-263">'Razor'</span></span>
- <span data-ttu-id="8b81e-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-264">'SignalR' uid:</span></span> 

<span data-ttu-id="8b81e-265">-------- |---title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-266">'Blazor'</span></span>
- <span data-ttu-id="8b81e-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-267">'Identity'</span></span>
- <span data-ttu-id="8b81e-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-269">'Razor'</span></span>
- <span data-ttu-id="8b81e-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-271">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-272">'Blazor'</span></span>
- <span data-ttu-id="8b81e-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-273">'Identity'</span></span>
- <span data-ttu-id="8b81e-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-275">'Razor'</span></span>
- <span data-ttu-id="8b81e-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8b81e-277">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="8b81e-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8b81e-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-278">'Blazor'</span></span>
- <span data-ttu-id="8b81e-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b81e-279">'Identity'</span></span>
- <span data-ttu-id="8b81e-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b81e-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b81e-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b81e-281">'Razor'</span></span>
- <span data-ttu-id="8b81e-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8b81e-282">'SignalR' uid:</span></span> 

<span data-ttu-id="8b81e-283">------ | |`Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>値を取得するために使用する。</span><span class="sxs-lookup"><span data-stu-id="8b81e-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="8b81e-284">| |`Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>シークレットの読み込みを制御するために使用されるインスタンス。</span><span class="sxs-lookup"><span data-stu-id="8b81e-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="8b81e-285">| |`ReloadInterval` | `Timespan`キーコンテナーのポーリングによって変更が試行されるまでの待機時間。</span><span class="sxs-lookup"><span data-stu-id="8b81e-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="8b81e-286">既定値はです `null` (構成は再読み込みされません)。</span><span class="sxs-lookup"><span data-stu-id="8b81e-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="8b81e-287">| |`Vault`          |Key vault URI。</span><span class="sxs-lookup"><span data-stu-id="8b81e-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8b81e-288">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-288">Use a key name prefix</span></span>

<span data-ttu-id="8b81e-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8b81e-290">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8b81e-291">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8b81e-292">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8b81e-293">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8b81e-294">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="8b81e-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8b81e-295">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8b81e-296">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8b81e-297">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8b81e-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8b81e-299">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8b81e-300">`Load`名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8b81e-301">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8b81e-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8b81e-302">`GetKey`:</span></span>
  * <span data-ttu-id="8b81e-303">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8b81e-304">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8b81e-305">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8b81e-306">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8b81e-307">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8b81e-308">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8b81e-309">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="8b81e-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="8b81e-310">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="8b81e-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8b81e-311">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8b81e-312">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8b81e-313">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8b81e-314">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8b81e-315">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8b81e-316">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8b81e-317">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8b81e-318">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8b81e-319">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8b81e-320">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8b81e-321">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8b81e-322">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8b81e-322">Bind an array to a class</span></span>

<span data-ttu-id="8b81e-323">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8b81e-324">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8b81e-325">詳細については、「[構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8b81e-326">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8b81e-327">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8b81e-328">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8b81e-329">JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8b81e-330">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この*シンク*には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="8b81e-331">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8b81e-332">キー</span><span class="sxs-lookup"><span data-stu-id="8b81e-332">Key</span></span> | <span data-ttu-id="8b81e-333">値</span><span class="sxs-lookup"><span data-stu-id="8b81e-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8b81e-334">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="8b81e-334">Reload secrets</span></span>

<span data-ttu-id="8b81e-335">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8b81e-336">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8b81e-337">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="8b81e-337">Disabled and expired secrets</span></span>

<span data-ttu-id="8b81e-338">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8b81e-339">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8b81e-340">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8b81e-340">Troubleshoot</span></span>

<span data-ttu-id="8b81e-341">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8b81e-342">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8b81e-343">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8b81e-344">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8b81e-345">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8b81e-346">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8b81e-347">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8b81e-348">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8b81e-349">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8b81e-350">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="8b81e-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b81e-351">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8b81e-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8b81e-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b81e-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8b81e-353">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="8b81e-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8b81e-354">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="8b81e-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8b81e-355">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="8b81e-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8b81e-356">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="8b81e-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8b81e-357">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="8b81e-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b81e-358">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8b81e-359">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8b81e-360">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8b81e-361">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="8b81e-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8b81e-362">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="8b81e-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8b81e-363">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8b81e-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8b81e-364">パッケージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-364">Packages</span></span>

<span data-ttu-id="8b81e-365">パッケージ[への参照を、パッケージへ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8b81e-366">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="8b81e-366">Sample app</span></span>

<span data-ttu-id="8b81e-367">サンプルアプリは、 `#define` *Program.cs*ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8b81e-368">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8b81e-369">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8b81e-370">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8b81e-371">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8b81e-372">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8b81e-373">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8b81e-374">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8b81e-375">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="8b81e-376">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8b81e-377">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8b81e-378">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8b81e-379">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8b81e-380">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8b81e-381">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8b81e-382">シークレットマネージャーは、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8b81e-383">プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8b81e-384">これらのシークレットが Azure Key Vault セクションを[含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8b81e-385">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8b81e-386">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="8b81e-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8b81e-387">クイックスタートに記載されている手順[: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli)する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8b81e-388">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8b81e-389">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8b81e-390">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8b81e-391">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8b81e-392">[ **Cloud Shell の起動**] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8b81e-393">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8b81e-394">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8b81e-395">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8b81e-396">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8b81e-397">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8b81e-398">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8b81e-399">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8b81e-400">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8b81e-401">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8b81e-402">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8b81e-403">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8b81e-404">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8b81e-405">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8b81e-406">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8b81e-407">**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8b81e-408">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8b81e-409">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8b81e-410">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8b81e-411">このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8b81e-412">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8b81e-413">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8b81e-414">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8b81e-415">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="8b81e-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8b81e-416">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8b81e-417">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8b81e-418">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8b81e-419">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8b81e-420">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8b81e-421">[**証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8b81e-422">公開キーを含む証明書をアップロードするには、[**証明書のアップロード**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8b81e-423">*.Cer*、 *pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8b81e-424">Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8b81e-425">Azure portal の**キーコンテナー**に移動します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8b81e-426">Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8b81e-427">[**アクセスポリシー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="8b81e-428">[**アクセスポリシーの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8b81e-429">[**シークレットのアクセス許可**] を開き、アプリに**Get**および**List**アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8b81e-430">[**プリンシパルの選択**] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8b81e-431">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="8b81e-432">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-432">Select **OK**.</span></span>
1. <span data-ttu-id="8b81e-433">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-433">Select **Save**.</span></span>
1. <span data-ttu-id="8b81e-434">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-434">Deploy the app.</span></span>

<span data-ttu-id="8b81e-435">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8b81e-436">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8b81e-437">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8b81e-438">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8b81e-439">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8b81e-440">アプリは、 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings*ファイルによって指定された値を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8b81e-441">値の例:</span><span class="sxs-lookup"><span data-stu-id="8b81e-441">Example values:</span></span>

* <span data-ttu-id="8b81e-442">Key vault 名:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8b81e-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8b81e-443">アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8b81e-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8b81e-444">証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8b81e-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8b81e-445">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b81e-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8b81e-446">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8b81e-447">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8b81e-448">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8b81e-449">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8b81e-450">**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8b81e-451">このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8b81e-452">アプリの*appsettings*ファイルにコンテナー名を入力します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8b81e-453">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8b81e-454">アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8b81e-455">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8b81e-456">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8b81e-457">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8b81e-458">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8b81e-459">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8b81e-460">Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8b81e-461">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="8b81e-461">The sample app:</span></span>

* <span data-ttu-id="8b81e-462">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8b81e-463">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8b81e-464"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8b81e-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8b81e-466">Key vault 名の値の例:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8b81e-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8b81e-467">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b81e-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8b81e-468">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8b81e-469">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8b81e-470">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8b81e-471">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8b81e-472">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8b81e-473">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「[管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8b81e-474">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="8b81e-474">Use a key name prefix</span></span>

<span data-ttu-id="8b81e-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8b81e-476">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8b81e-477">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8b81e-478">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8b81e-479">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8b81e-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8b81e-480">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="8b81e-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8b81e-481">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8b81e-482">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8b81e-483">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8b81e-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8b81e-485">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8b81e-486">`Load`名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8b81e-487">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8b81e-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8b81e-488">`GetKey`:</span></span>
  * <span data-ttu-id="8b81e-489">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8b81e-490">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8b81e-491">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8b81e-492">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8b81e-493">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8b81e-494">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8b81e-495">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="8b81e-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="8b81e-496">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="8b81e-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8b81e-497">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8b81e-498">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="8b81e-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8b81e-499">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8b81e-500">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8b81e-501">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8b81e-502">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8b81e-503">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8b81e-504">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8b81e-505">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="8b81e-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8b81e-506">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8b81e-507">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8b81e-508">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8b81e-508">Bind an array to a class</span></span>

<span data-ttu-id="8b81e-509">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8b81e-510">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8b81e-511">詳細については、「[構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8b81e-512">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8b81e-513">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8b81e-514">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8b81e-515">JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="8b81e-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8b81e-516">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この*シンク*には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="8b81e-517">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8b81e-518">キー</span><span class="sxs-lookup"><span data-stu-id="8b81e-518">Key</span></span> | <span data-ttu-id="8b81e-519">値</span><span class="sxs-lookup"><span data-stu-id="8b81e-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8b81e-520">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="8b81e-520">Reload secrets</span></span>

<span data-ttu-id="8b81e-521">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8b81e-522">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8b81e-523">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="8b81e-523">Disabled and expired secrets</span></span>

<span data-ttu-id="8b81e-524">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8b81e-525">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="8b81e-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8b81e-526">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8b81e-526">Troubleshoot</span></span>

<span data-ttu-id="8b81e-527">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8b81e-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8b81e-528">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8b81e-529">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8b81e-530">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8b81e-531">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8b81e-532">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="8b81e-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8b81e-533">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="8b81e-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8b81e-534">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8b81e-535">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="8b81e-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8b81e-536">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="8b81e-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b81e-537">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8b81e-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8b81e-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b81e-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8b81e-539">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="8b81e-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8b81e-540">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="8b81e-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8b81e-541">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="8b81e-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8b81e-542">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="8b81e-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8b81e-543">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="8b81e-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

