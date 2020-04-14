---
title: ASP.NET コアの Azure キー コンテナー構成プロバイダー
author: rick-anderson
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれた名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228167"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="6356c-103">ASP.NET コアの Azure キー コンテナー構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="6356c-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="6356c-104">[アンドリュー・スタントン=ナース](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6356c-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6356c-105">このドキュメントでは[、Microsoft Azure キー コンテナー](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6356c-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="6356c-106">Azure Key Vault は、アプリやサービスで使用される暗号化キーとシークレットの保護を支援するクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="6356c-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="6356c-107">Azure Key Vault を ASP.NET コア アプリで使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6356c-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="6356c-108">機密構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="6356c-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="6356c-109">構成データを格納する際に FIPS 140-2 レベル 2 の検証済みハードウェア セキュリティ モジュール (HSM) の要件を満たす。</span><span class="sxs-lookup"><span data-stu-id="6356c-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="6356c-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6356c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="6356c-111">パッケージ</span><span class="sxs-lookup"><span data-stu-id="6356c-111">Packages</span></span>

<span data-ttu-id="6356c-112">パッケージ参照を[追加](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)します。</span><span class="sxs-lookup"><span data-stu-id="6356c-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6356c-113">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="6356c-113">Sample app</span></span>

<span data-ttu-id="6356c-114">サンプル アプリは`#define`*、Program.cs*ファイルの先頭にあるステートメントによって決まる 2 つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="6356c-115">`Certificate`&ndash; Azure Key コンテナーに格納されているシークレットにアクセスするための Azure キー コンテナー クライアント ID と X.509 証明書の使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6356c-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="6356c-116">このバージョンのサンプルは、Azure App Service にデプロイされた任意の場所または ASP.NET コア アプリを提供できる任意のホストから実行できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="6356c-117">`Managed`&ndash; [Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に資格情報を格納せずに Azure AD 認証を使用して Azure Key Vault に対してアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6356c-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="6356c-118">マネージド ID を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="6356c-119">サンプル`Managed`のバージョンは Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6356c-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="6356c-120">「Azure リソースに[管理対象 ID を使用する」セクションのガイダンスに](#use-managed-identities-for-azure-resources)従います。</span><span class="sxs-lookup"><span data-stu-id="6356c-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="6356c-121">プリプロセッサ ディレクティブ ( )`#define`を使用してサンプル アプリを構成する<xref:index#preprocessor-directives-in-sample-code>方法の詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="6356c-122">開発環境における秘密ストレージ</span><span class="sxs-lookup"><span data-stu-id="6356c-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="6356c-123">[シークレットマネージャツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6356c-124">サンプル アプリが開発環境のローカル コンピューターで実行されると、シークレットはローカルシークレット マネージャー ストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="6356c-125">シークレット マネージャー ツールでは`<UserSecretsId>`、アプリのプロジェクト ファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="6356c-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="6356c-126">プロパティ値 (`{GUID}`) を一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="6356c-127">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="6356c-128">階層値 (構成セクション)`:`では[、ASP.NETコア コンフィギュレーション](xref:fundamentals/configuration/index)キー名の区切り記号としてコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="6356c-129">Secret Manager は、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に`{SECRET NAME}``{SECRET VALUE}`開かれたコマンドシェルから使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="6356c-130">プロジェクトの[コンテンツ ルート](xref:fundamentals/index#content-root)からコマンド シェルで次のコマンドを実行して、サンプル アプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="6356c-131">これらのシークレットが [Azure Key Vault と共に運用環境] の[シークレット ストレージの Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault)に格納されている場合、`_dev`サフィックスは に`_prod`変更されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="6356c-132">サフィックスは、構成値のソースを示すアプリの出力に視覚的な手掛かりを提供します。</span><span class="sxs-lookup"><span data-stu-id="6356c-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="6356c-133">Azure Key Vault を使用した運用環境でのシークレット ストレージ</span><span class="sxs-lookup"><span data-stu-id="6356c-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="6356c-134">[「クイック スタート: Azure CLI を使用して Azure Key Vault からシークレットを設定および取得](/azure/key-vault/quick-create-cli)する」トピックは、Azure Key Vault を作成し、サンプル アプリで使用されるシークレットを格納する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6356c-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="6356c-135">詳細については、トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="6356c-136">Azure ポータルで次のいずれかの方法を使用して[、Azure](https://portal.azure.com/)クラウド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="6356c-137">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="6356c-138">テキスト ボックスの検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="6356c-139">[クラウド シェルの起動] ボタンを使用して、ブラウザーで **[クラウド シェル**] を開きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="6356c-140">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="6356c-141">詳細については[、「Azure CLI」](/cli/azure/)および[「Azure クラウド シェルの概要](/azure/cloud-shell/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="6356c-142">認証されていない場合は、`az login`コマンドを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="6356c-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="6356c-143">次のコマンドでリソース グループを作成します`{RESOURCE GROUP NAME}`。 `{LOCATION}`</span><span class="sxs-lookup"><span data-stu-id="6356c-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6356c-144">次のコマンドを使用して、`{KEY VAULT NAME}`リソース グループにキー コンテナー`{LOCATION}`を作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6356c-145">キー コンテナーに名前と値のペアとしてシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="6356c-146">Azure Key Vault シークレット名は、英数字とダッシュに制限されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="6356c-147">階層値 (構成セクション)`--`は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6356c-148">通常[、ASP.NETコア構成](xref:fundamentals/configuration/index)のサブキーからセクションを区切るために使用されるコロンは、キー コンテナーシークレット名では使用できません。</span><span class="sxs-lookup"><span data-stu-id="6356c-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="6356c-149">したがって、シークレットがアプリの構成に読み込まれると、2 つのダッシュが使用され、コロンに交換されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="6356c-150">サンプル アプリでは、次のシークレットが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="6356c-151">値には、`_prod`開発環境で読み込まれたユーザー`_dev`シークレットとのサフィックス値と区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="6356c-152">前`{KEY VAULT NAME}`の手順で作成したキー コンテナーの名前で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6356c-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="6356c-153">Azure ホスト以外のアプリにアプリケーション ID と X.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="6356c-154">Azure の**外部でアプリがホストされている場合**、Azure AD、Azure キー コンテナー、およびアプリがキー コンテナーに対して認証するために Azure Active Directory アプリケーション ID と X.509 証明書を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="6356c-155">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="6356c-156">Azure でホストされているアプリではアプリケーション ID と X.509 証明書の使用がサポートされていますが、Azure でアプリをホストする場合は[、Azure リソースのマネージド ID](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6356c-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="6356c-157">管理対象 ID では、アプリや開発環境に証明書を格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="6356c-158">Program.cs*ファイルの*先頭にある`Certificate``#define`ステートメントが に設定されている場合、サンプル アプリはアプリケーション ID と X.509 証明書を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="6356c-159">PKCS#12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="6356c-160">証明書を作成するためのオプションには[、Windows の MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)があります。</span><span class="sxs-lookup"><span data-stu-id="6356c-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="6356c-161">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6356c-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="6356c-162">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="6356c-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="6356c-163">このプロセスの後半で使用される証明書の拇印をメモします。</span><span class="sxs-lookup"><span data-stu-id="6356c-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="6356c-164">PKCS#12 アーカイブ (*.pfx*) 証明書を DER エンコード証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="6356c-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="6356c-165">Azure AD (**アプリの登録) にアプリを登録**します。</span><span class="sxs-lookup"><span data-stu-id="6356c-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="6356c-166">DER エンコード証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="6356c-167">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="6356c-168">**[証明書& シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="6356c-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="6356c-169">[**証明書のアップロード**] を選択して、公開キーを含む証明書をアップロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="6356c-170">*cer* *、.pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="6356c-171">キー コンテナー名、アプリケーション ID、および証明書の拇印をアプリの*appsettings.json*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="6356c-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="6356c-172">Azure ポータルの **[キー コンテナー** ] に移動します。</span><span class="sxs-lookup"><span data-stu-id="6356c-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="6356c-173">[Azure Key Vault を使用した運用環境] セクション[のシークレット ストレージで作成したキー コンテナーを](#secret-storage-in-the-production-environment-with-azure-key-vault)選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="6356c-174">[**アクセス ポリシー ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="6356c-175">[**アクセス ポリシーの追加 ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="6356c-176">**シークレットのアクセス許可**を開き **、Get**と**List**のアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="6356c-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="6356c-177">[**プリンシパルを選択**] を選択し、登録されているアプリを名前で選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="6356c-178">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="6356c-179">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-179">Select **OK**.</span></span>
1. <span data-ttu-id="6356c-180">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-180">Select **Save**.</span></span>
1. <span data-ttu-id="6356c-181">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="6356c-181">Deploy the app.</span></span>

<span data-ttu-id="6356c-182">サンプル`Certificate`アプリは、シークレット名`IConfigurationRoot`と同じ名前の構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="6356c-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="6356c-183">非階層値: の値`SecretName`は`config["SecretName"]`で取得されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="6356c-184">階層値 (セクション): `:` (コロン) 表記または`GetSection`拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="6356c-185">構成値を取得するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="6356c-186">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="6356c-187">アプリは<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>*、appsettings.json*ファイルによって指定された値を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6356c-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="6356c-188">サンプル値: </span><span class="sxs-lookup"><span data-stu-id="6356c-188">Example values:</span></span>

* <span data-ttu-id="6356c-189">キー コンテナー名:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="6356c-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="6356c-190">アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="6356c-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="6356c-191">証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="6356c-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="6356c-192">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6356c-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="6356c-193">アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6356c-194">開発環境では、シークレット値が接尾辞とともに`_dev`ロードされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="6356c-195">実稼働環境では、値に接尾辞`_prod`が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="6356c-196">Azure リソースにマネージド ID を使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="6356c-197">**Azure にデプロイされたアプリは**[、Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を利用して、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアント シークレット) なしで Azure AD 認証を使用して Azure Key Vault で認証できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="6356c-198">Program.cs*ファイルの*上部にあるステートメントが`#define`に設定されている場合、サンプル アプリは Azure リソース`Managed`のマネージド ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="6356c-199">アプリの*appsettings.json*ファイルにボルト名を入力します。</span><span class="sxs-lookup"><span data-stu-id="6356c-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="6356c-200">サンプル アプリでは、`Managed`バージョンに設定されているアプリケーション ID とパスワード (クライアント シークレット) は必要ないので、これらの構成エントリは無視できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="6356c-201">アプリは Azure にデプロイされ、Azure は*appsettings.json*ファイルに格納されているコンテナー名のみを使用して Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="6356c-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="6356c-202">サンプル アプリを Azure アプリ サービスにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="6356c-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="6356c-203">Azure アプリ サービスにデプロイされたアプリは、サービスの作成時に自動的に Azure AD に登録されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="6356c-204">次のコマンドで使用する展開からオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="6356c-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="6356c-205">オブジェクト ID は、Azure ポータルの App Service の **[ID]** パネルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="6356c-206">Azure CLI とアプリのオブジェクト ID を使用して、`list`アプリ`get`に、キー コンテナーにアクセスするためのアクセス許可とを指定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="6356c-207">Azure CLI、PowerShell、または Azure ポータルを使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="6356c-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="6356c-208">サンプル アプリ:</span><span class="sxs-lookup"><span data-stu-id="6356c-208">The sample app:</span></span>

* <span data-ttu-id="6356c-209">接続文字列を使用せずにクラス`AzureServiceTokenProvider`のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="6356c-210">接続文字列が指定されていない場合、プロバイダーは Azure リソースのマネージ ID からアクセス トークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="6356c-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="6356c-211">インスタンストークン<xref:Microsoft.Azure.KeyVault.KeyVaultClient>コールバックを使用して`AzureServiceTokenProvider`新しいコードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="6356c-212">インスタンス<xref:Microsoft.Azure.KeyVault.KeyVaultClient>は、すべてのシークレット値をロード<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>し、二重ダッシュ ( ) をキー名の`--`コロン (`:`) に置き換えるデフォルトの実装で使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="6356c-213">キー コンテナー名の値の例:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="6356c-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="6356c-214">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6356c-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="6356c-215">アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6356c-216">開発環境では、シークレット値にはユーザー シークレット`_dev`によって提供されるため、サフィックスが付きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="6356c-217">実稼働環境では、値は Azure `_prod` Key Vault によって提供されるため、サフィックスと共に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="6356c-218">エラーが発生した`Access denied`場合は、アプリが Azure AD に登録され、キー コンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="6356c-219">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="6356c-220">マネージド ID と Azure DevOps パイプラインを持つプロバイダーの使用については、「[マネージド サービス ID を使用した VM への Azure リソース マネージャー サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="6356c-221">構成オプション</span><span class="sxs-lookup"><span data-stu-id="6356c-221">Configuration options</span></span>

<span data-ttu-id="6356c-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions></span><span class="sxs-lookup"><span data-stu-id="6356c-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="6356c-223">プロパティ</span><span class="sxs-lookup"><span data-stu-id="6356c-223">Property</span></span>         | <span data-ttu-id="6356c-224">説明</span><span class="sxs-lookup"><span data-stu-id="6356c-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="6356c-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>を使用して値を取得します。</span><span class="sxs-lookup"><span data-stu-id="6356c-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="6356c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>シークレットの読み込みを制御するために使用されるインスタンス。</span><span class="sxs-lookup"><span data-stu-id="6356c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="6356c-227">`Timespan`キー コンテナーのポーリングの試行の間に、変更を求める時間を待機します。</span><span class="sxs-lookup"><span data-stu-id="6356c-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="6356c-228">既定値は (`null`構成は再読み込みされません) です。</span><span class="sxs-lookup"><span data-stu-id="6356c-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="6356c-229">キー コンテナー URI。</span><span class="sxs-lookup"><span data-stu-id="6356c-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="6356c-230">キー名プレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-230">Use a key name prefix</span></span>

<span data-ttu-id="6356c-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されており、キー コンテナーシークレットをコンフィギュレーション キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="6356c-232">たとえば、アプリの起動時に指定したプレフィックス値に基づいてシークレット値を読み込むインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="6356c-233">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6356c-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="6356c-234">複数のアプリのシークレットを同じキー コンテナーに配置したり、環境の秘密 (*開発*と*運用*の秘密など) を同じコンテナーに配置したりするために、キー コンテナーシークレットのプレフィックスを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6356c-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="6356c-235">さまざまなアプリや開発環境や開発環境では、セキュリティのレベルが高いアプリ環境を分離するために、別々のキー コンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6356c-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="6356c-236">次の例では、シークレットがキー コンテナーに (および開発環境用の Secret Manager ツールを使用して`5000-AppSecret`) (キー コンテナーシークレット名では許可されない) の間にシークレットが確立されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="6356c-237">このシークレットは、アプリのバージョン 5.0.0.0 のアプリ シークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="6356c-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="6356c-238">アプリの別のバージョンである 5.1.0.0 の場合、シークレットが キー コンテナーに追加されます (およびシークレット`5100-AppSecret`マネージャー ツールを使用して) の .</span><span class="sxs-lookup"><span data-stu-id="6356c-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="6356c-239">各アプリのバージョンは、バージョン管理されたシークレット値をその`AppSecret`構成にロードし、シークレットをロードする際にバージョンを取り除く。</span><span class="sxs-lookup"><span data-stu-id="6356c-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="6356c-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタム<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="6356c-241">実装<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>は、シークレットのバージョンプレフィックスに反応して、適切なシークレットを設定にロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="6356c-242">`Load`名前がプレフィックスで始まると、シークレットがロードされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="6356c-243">他のシークレットは読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="6356c-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="6356c-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="6356c-244">`GetKey`:</span></span>
  * <span data-ttu-id="6356c-245">秘密名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="6356c-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="6356c-246">任意の名前の 2 つのダッシュを`KeyDelimiter`、構成で使用される区切り文字 (通常はコロン) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6356c-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="6356c-247">Azure Key Vault では、シークレット名のコロンは許可されません。</span><span class="sxs-lookup"><span data-stu-id="6356c-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="6356c-248">この`Load`メソッドは、バージョンプレフィックスを持つものを検索するために、コンテナーシークレットを反復処理するプロバイダー アルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="6356c-249">でバージョン接頭辞が見`Load`つかると、アルゴリズムは`GetKey`メソッドを使用してシークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="6356c-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="6356c-250">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むシークレット名の残りの部分を返します。</span><span class="sxs-lookup"><span data-stu-id="6356c-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="6356c-251">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="6356c-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="6356c-252">アプリのプロジェクト ファイルで指定されたアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="6356c-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="6356c-253">次の例では、アプリのバージョンが に`5.0.0.0`設定されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6356c-254">`<UserSecretsId>`プロパティがアプリのプロジェクト ファイルに存在することを確認します。 `{GUID}`</span><span class="sxs-lookup"><span data-stu-id="6356c-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="6356c-255">[シークレットマネージャツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="6356c-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="6356c-256">シークレットは、次の Azure CLI コマンドを使用して Azure キー コンテナーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="6356c-257">アプリが実行されると、キー コンテナーシークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="6356c-258">文字列のシークレット`5000-AppSecret`は、アプリのプロジェクト ファイル ( )`5.0.0.0`で指定されているアプリのバージョンと一致します。</span><span class="sxs-lookup"><span data-stu-id="6356c-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="6356c-259">`5000`バージョン (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="6356c-260">アプリ全体で、キー`AppSecret`を使用して設定を読み込むと、シークレット値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="6356c-261">アプリのバージョンがプロジェクト ファイルでに変更`5.1.0.0`され、アプリが再度実行される場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境と`5.1.0.0_secret_value_prod`運用環境です。</span><span class="sxs-lookup"><span data-stu-id="6356c-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="6356c-262">独自の<xref:Microsoft.Azure.KeyVault.KeyVaultClient>実装を<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="6356c-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="6356c-263">カスタム クライアントは、アプリ全体でクライアントの単一インスタンスを共有できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6356c-264">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="6356c-264">Bind an array to a class</span></span>

<span data-ttu-id="6356c-265">プロバイダーは、構成値を配列に読み取って POCO 配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="6356c-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="6356c-266">キーにコロン`:`( ) 区切り記号を含める構成ソースから読み取る場合、配列を構成するキー (`:0:`, `:1:`、 &hellip; `:{n}:`) を区別するために、数値キー セグメントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="6356c-267">詳細については、「[構成 : 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="6356c-268">Azure Key Vault キーでは、区切り記号としてコロンを使用できません。</span><span class="sxs-lookup"><span data-stu-id="6356c-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="6356c-269">このトピックで説明する方法では、階層値`--`(セクション) の区切り文字として二重ダッシュ ( ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="6356c-270">配列キーは、二重ダッシュと数値キー セグメント`--0--`( , )`--1--`&hellip;`--{n}--`を使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="6356c-271">JSON ファイルによって提供される次[の Serilog](https://serilog.net/)ログ プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="6356c-272">出力を記録する出力の出力先を`WriteTo`記述する 2 つの Serilog*シンク*を表す 2 つのオブジェクト リテラルが配列に定義されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="6356c-273">上記の JSON ファイルに示されている構成は、ダブル ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="6356c-274">Key</span><span class="sxs-lookup"><span data-stu-id="6356c-274">Key</span></span> | <span data-ttu-id="6356c-275">値</span><span class="sxs-lookup"><span data-stu-id="6356c-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="6356c-276">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="6356c-276">Reload secrets</span></span>

<span data-ttu-id="6356c-277">シークレットは呼び出`IConfigurationRoot.Reload()`されるまでキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="6356c-278">キー コンテナー内の期限切れ、無効、および更新されたシークレットは、実行`Reload`されるまでアプリによっては尊重されません。</span><span class="sxs-lookup"><span data-stu-id="6356c-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="6356c-279">無効と期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="6356c-279">Disabled and expired secrets</span></span>

<span data-ttu-id="6356c-280">無効にされたシークレットと期限<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>切れのシークレットは、 .</span><span class="sxs-lookup"><span data-stu-id="6356c-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="6356c-281">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新します。</span><span class="sxs-lookup"><span data-stu-id="6356c-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6356c-282">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6356c-282">Troubleshoot</span></span>

<span data-ttu-id="6356c-283">アプリがプロバイダーを使用して構成を読み込めないと、エラー メッセージが[ASP.NET コア ログ インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6356c-284">次の条件により、構成の読み込みができなくなります。</span><span class="sxs-lookup"><span data-stu-id="6356c-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="6356c-285">アプリまたは証明書が Azure アクティブ ディレクトリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="6356c-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="6356c-286">キー コンテナーが Azure キー コンテナーに存在しません。</span><span class="sxs-lookup"><span data-stu-id="6356c-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="6356c-287">アプリは、キー コンテナーにアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="6356c-288">アクセス ポリシーには、アクセス許可`Get`は`List`含みません。</span><span class="sxs-lookup"><span data-stu-id="6356c-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="6356c-289">キー コンテナーで、構成データ (名前と値のペア) の名前が正しくない、見つからない、無効にする、または期限切れです。</span><span class="sxs-lookup"><span data-stu-id="6356c-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="6356c-290">アプリのキー コンテナー名 (`KeyVaultName`)、Azure AD`AzureADApplicationId`アプリケーション ID ( )、`AzureADCertThumbprint`または Azure AD 証明書の拇印 ( ) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="6356c-291">アプリで、読み込もうとしている値のコンフィギュレーション キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="6356c-292">アプリのアクセス ポリシーをキー コンテナーに追加する場合、ポリシーは作成されましたが、**アクセス ポリシー** UI で **[保存]** ボタンが選択されていません。</span><span class="sxs-lookup"><span data-stu-id="6356c-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6356c-293">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6356c-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6356c-294">マイクロソフト Azure: キー コンテナー</span><span class="sxs-lookup"><span data-stu-id="6356c-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="6356c-295">マイクロソフト Azure: キー コンテナー のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6356c-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="6356c-296">Azure Key Vault の HSM で保護されたキーを生成および転送する方法</span><span class="sxs-lookup"><span data-stu-id="6356c-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="6356c-297">クラス</span><span class="sxs-lookup"><span data-stu-id="6356c-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="6356c-298">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="6356c-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="6356c-299">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="6356c-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6356c-300">このドキュメントでは[、Microsoft Azure キー コンテナー](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6356c-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="6356c-301">Azure Key Vault は、アプリやサービスで使用される暗号化キーとシークレットの保護を支援するクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="6356c-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="6356c-302">Azure Key Vault を ASP.NET コア アプリで使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6356c-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="6356c-303">機密構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="6356c-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="6356c-304">構成データを格納する際に FIPS 140-2 レベル 2 の検証済みハードウェア セキュリティ モジュール (HSM) の要件を満たす。</span><span class="sxs-lookup"><span data-stu-id="6356c-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="6356c-305">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6356c-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="6356c-306">パッケージ</span><span class="sxs-lookup"><span data-stu-id="6356c-306">Packages</span></span>

<span data-ttu-id="6356c-307">パッケージ参照を[追加](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)します。</span><span class="sxs-lookup"><span data-stu-id="6356c-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6356c-308">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="6356c-308">Sample app</span></span>

<span data-ttu-id="6356c-309">サンプル アプリは`#define`*、Program.cs*ファイルの先頭にあるステートメントによって決まる 2 つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="6356c-310">`Certificate`&ndash; Azure Key コンテナーに格納されているシークレットにアクセスするための Azure キー コンテナー クライアント ID と X.509 証明書の使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6356c-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="6356c-311">このバージョンのサンプルは、Azure App Service にデプロイされた任意の場所または ASP.NET コア アプリを提供できる任意のホストから実行できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="6356c-312">`Managed`&ndash; [Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に資格情報を格納せずに Azure AD 認証を使用して Azure Key Vault に対してアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6356c-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="6356c-313">マネージド ID を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="6356c-314">サンプル`Managed`のバージョンは Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6356c-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="6356c-315">「Azure リソースに[管理対象 ID を使用する」セクションのガイダンスに](#use-managed-identities-for-azure-resources)従います。</span><span class="sxs-lookup"><span data-stu-id="6356c-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="6356c-316">プリプロセッサ ディレクティブ ( )`#define`を使用してサンプル アプリを構成する<xref:index#preprocessor-directives-in-sample-code>方法の詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="6356c-317">開発環境における秘密ストレージ</span><span class="sxs-lookup"><span data-stu-id="6356c-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="6356c-318">[シークレットマネージャツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6356c-319">サンプル アプリが開発環境のローカル コンピューターで実行されると、シークレットはローカルシークレット マネージャー ストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="6356c-320">シークレット マネージャー ツールでは`<UserSecretsId>`、アプリのプロジェクト ファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="6356c-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="6356c-321">プロパティ値 (`{GUID}`) を一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="6356c-322">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="6356c-323">階層値 (構成セクション)`:`では[、ASP.NETコア コンフィギュレーション](xref:fundamentals/configuration/index)キー名の区切り記号としてコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="6356c-324">Secret Manager は、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に`{SECRET NAME}``{SECRET VALUE}`開かれたコマンドシェルから使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="6356c-325">プロジェクトの[コンテンツ ルート](xref:fundamentals/index#content-root)からコマンド シェルで次のコマンドを実行して、サンプル アプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="6356c-326">これらのシークレットが [Azure Key Vault と共に運用環境] の[シークレット ストレージの Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault)に格納されている場合、`_dev`サフィックスは に`_prod`変更されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="6356c-327">サフィックスは、構成値のソースを示すアプリの出力に視覚的な手掛かりを提供します。</span><span class="sxs-lookup"><span data-stu-id="6356c-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="6356c-328">Azure Key Vault を使用した運用環境でのシークレット ストレージ</span><span class="sxs-lookup"><span data-stu-id="6356c-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="6356c-329">[「クイック スタート: Azure CLI を使用して Azure Key Vault からシークレットを設定および取得](/azure/key-vault/quick-create-cli)する」トピックは、Azure Key Vault を作成し、サンプル アプリで使用されるシークレットを格納する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6356c-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="6356c-330">詳細については、トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="6356c-331">Azure ポータルで次のいずれかの方法を使用して[、Azure](https://portal.azure.com/)クラウド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="6356c-332">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="6356c-333">テキスト ボックスの検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="6356c-334">[クラウド シェルの起動] ボタンを使用して、ブラウザーで **[クラウド シェル**] を開きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="6356c-335">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="6356c-336">詳細については[、「Azure CLI」](/cli/azure/)および[「Azure クラウド シェルの概要](/azure/cloud-shell/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="6356c-337">認証されていない場合は、`az login`コマンドを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="6356c-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="6356c-338">次のコマンドでリソース グループを作成します`{RESOURCE GROUP NAME}`。 `{LOCATION}`</span><span class="sxs-lookup"><span data-stu-id="6356c-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6356c-339">次のコマンドを使用して、`{KEY VAULT NAME}`リソース グループにキー コンテナー`{LOCATION}`を作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6356c-340">キー コンテナーに名前と値のペアとしてシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="6356c-341">Azure Key Vault シークレット名は、英数字とダッシュに制限されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="6356c-342">階層値 (構成セクション)`--`は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6356c-343">通常[、ASP.NETコア構成](xref:fundamentals/configuration/index)のサブキーからセクションを区切るために使用されるコロンは、キー コンテナーシークレット名では使用できません。</span><span class="sxs-lookup"><span data-stu-id="6356c-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="6356c-344">したがって、シークレットがアプリの構成に読み込まれると、2 つのダッシュが使用され、コロンに交換されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="6356c-345">サンプル アプリでは、次のシークレットが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="6356c-346">値には、`_prod`開発環境で読み込まれたユーザー`_dev`シークレットとのサフィックス値と区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="6356c-347">前`{KEY VAULT NAME}`の手順で作成したキー コンテナーの名前で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6356c-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="6356c-348">Azure ホスト以外のアプリにアプリケーション ID と X.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="6356c-349">Azure の**外部でアプリがホストされている場合**、Azure AD、Azure キー コンテナー、およびアプリがキー コンテナーに対して認証するために Azure Active Directory アプリケーション ID と X.509 証明書を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="6356c-350">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="6356c-351">Azure でホストされているアプリではアプリケーション ID と X.509 証明書の使用がサポートされていますが、Azure でアプリをホストする場合は[、Azure リソースのマネージド ID](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6356c-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="6356c-352">管理対象 ID では、アプリや開発環境に証明書を格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="6356c-353">Program.cs*ファイルの*先頭にある`Certificate``#define`ステートメントが に設定されている場合、サンプル アプリはアプリケーション ID と X.509 証明書を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="6356c-354">PKCS#12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="6356c-355">証明書を作成するためのオプションには[、Windows の MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)があります。</span><span class="sxs-lookup"><span data-stu-id="6356c-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="6356c-356">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6356c-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="6356c-357">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="6356c-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="6356c-358">このプロセスの後半で使用される証明書の拇印をメモします。</span><span class="sxs-lookup"><span data-stu-id="6356c-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="6356c-359">PKCS#12 アーカイブ (*.pfx*) 証明書を DER エンコード証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="6356c-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="6356c-360">Azure AD (**アプリの登録) にアプリを登録**します。</span><span class="sxs-lookup"><span data-stu-id="6356c-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="6356c-361">DER エンコード証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="6356c-362">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="6356c-363">**[証明書& シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="6356c-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="6356c-364">[**証明書のアップロード**] を選択して、公開キーを含む証明書をアップロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="6356c-365">*cer* *、.pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="6356c-366">キー コンテナー名、アプリケーション ID、および証明書の拇印をアプリの*appsettings.json*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="6356c-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="6356c-367">Azure ポータルの **[キー コンテナー** ] に移動します。</span><span class="sxs-lookup"><span data-stu-id="6356c-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="6356c-368">[Azure Key Vault を使用した運用環境] セクション[のシークレット ストレージで作成したキー コンテナーを](#secret-storage-in-the-production-environment-with-azure-key-vault)選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="6356c-369">[**アクセス ポリシー ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="6356c-370">[**アクセス ポリシーの追加 ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="6356c-371">**シークレットのアクセス許可**を開き **、Get**と**List**のアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="6356c-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="6356c-372">[**プリンシパルを選択**] を選択し、登録されているアプリを名前で選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="6356c-373">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="6356c-374">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-374">Select **OK**.</span></span>
1. <span data-ttu-id="6356c-375">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6356c-375">Select **Save**.</span></span>
1. <span data-ttu-id="6356c-376">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="6356c-376">Deploy the app.</span></span>

<span data-ttu-id="6356c-377">サンプル`Certificate`アプリは、シークレット名`IConfigurationRoot`と同じ名前の構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="6356c-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="6356c-378">非階層値: の値`SecretName`は`config["SecretName"]`で取得されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="6356c-379">階層値 (セクション): `:` (コロン) 表記または`GetSection`拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="6356c-380">構成値を取得するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="6356c-381">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="6356c-382">アプリは<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>*、appsettings.json*ファイルによって指定された値を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6356c-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="6356c-383">サンプル値: </span><span class="sxs-lookup"><span data-stu-id="6356c-383">Example values:</span></span>

* <span data-ttu-id="6356c-384">キー コンテナー名:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="6356c-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="6356c-385">アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="6356c-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="6356c-386">証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="6356c-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="6356c-387">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6356c-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="6356c-388">アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6356c-389">開発環境では、シークレット値が接尾辞とともに`_dev`ロードされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="6356c-390">実稼働環境では、値に接尾辞`_prod`が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="6356c-391">Azure リソースにマネージド ID を使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="6356c-392">**Azure にデプロイされたアプリは**[、Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を利用して、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアント シークレット) なしで Azure AD 認証を使用して Azure Key Vault で認証できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="6356c-393">Program.cs*ファイルの*上部にあるステートメントが`#define`に設定されている場合、サンプル アプリは Azure リソース`Managed`のマネージド ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="6356c-394">アプリの*appsettings.json*ファイルにボルト名を入力します。</span><span class="sxs-lookup"><span data-stu-id="6356c-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="6356c-395">サンプル アプリでは、`Managed`バージョンに設定されているアプリケーション ID とパスワード (クライアント シークレット) は必要ないので、これらの構成エントリは無視できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="6356c-396">アプリは Azure にデプロイされ、Azure は*appsettings.json*ファイルに格納されているコンテナー名のみを使用して Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="6356c-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="6356c-397">サンプル アプリを Azure アプリ サービスにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="6356c-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="6356c-398">Azure アプリ サービスにデプロイされたアプリは、サービスの作成時に自動的に Azure AD に登録されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="6356c-399">次のコマンドで使用する展開からオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="6356c-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="6356c-400">オブジェクト ID は、Azure ポータルの App Service の **[ID]** パネルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="6356c-401">Azure CLI とアプリのオブジェクト ID を使用して、`list`アプリ`get`に、キー コンテナーにアクセスするためのアクセス許可とを指定します。</span><span class="sxs-lookup"><span data-stu-id="6356c-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="6356c-402">Azure CLI、PowerShell、または Azure ポータルを使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="6356c-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="6356c-403">サンプル アプリ:</span><span class="sxs-lookup"><span data-stu-id="6356c-403">The sample app:</span></span>

* <span data-ttu-id="6356c-404">接続文字列を使用せずにクラス`AzureServiceTokenProvider`のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="6356c-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="6356c-405">接続文字列が指定されていない場合、プロバイダーは Azure リソースのマネージ ID からアクセス トークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="6356c-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="6356c-406">インスタンストークン<xref:Microsoft.Azure.KeyVault.KeyVaultClient>コールバックを使用して`AzureServiceTokenProvider`新しいコードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="6356c-407">インスタンス<xref:Microsoft.Azure.KeyVault.KeyVaultClient>は、すべてのシークレット値をロード<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>し、二重ダッシュ ( ) をキー名の`--`コロン (`:`) に置き換えるデフォルトの実装で使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="6356c-408">キー コンテナー名の値の例:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="6356c-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="6356c-409">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6356c-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="6356c-410">アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6356c-411">開発環境では、シークレット値にはユーザー シークレット`_dev`によって提供されるため、サフィックスが付きます。</span><span class="sxs-lookup"><span data-stu-id="6356c-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="6356c-412">実稼働環境では、値は Azure `_prod` Key Vault によって提供されるため、サフィックスと共に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="6356c-413">エラーが発生した`Access denied`場合は、アプリが Azure AD に登録され、キー コンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="6356c-414">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="6356c-415">マネージド ID と Azure DevOps パイプラインを持つプロバイダーの使用については、「[マネージド サービス ID を使用した VM への Azure リソース マネージャー サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="6356c-416">キー名プレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="6356c-416">Use a key name prefix</span></span>

<span data-ttu-id="6356c-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されており、キー コンテナーシークレットをコンフィギュレーション キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="6356c-418">たとえば、アプリの起動時に指定したプレフィックス値に基づいてシークレット値を読み込むインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="6356c-419">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="6356c-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="6356c-420">複数のアプリのシークレットを同じキー コンテナーに配置したり、環境の秘密 (*開発*と*運用*の秘密など) を同じコンテナーに配置したりするために、キー コンテナーシークレットのプレフィックスを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6356c-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="6356c-421">さまざまなアプリや開発環境や開発環境では、セキュリティのレベルが高いアプリ環境を分離するために、別々のキー コンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6356c-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="6356c-422">次の例では、シークレットがキー コンテナーに (および開発環境用の Secret Manager ツールを使用して`5000-AppSecret`) (キー コンテナーシークレット名では許可されない) の間にシークレットが確立されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="6356c-423">このシークレットは、アプリのバージョン 5.0.0.0 のアプリ シークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="6356c-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="6356c-424">アプリの別のバージョンである 5.1.0.0 の場合、シークレットが キー コンテナーに追加されます (およびシークレット`5100-AppSecret`マネージャー ツールを使用して) の .</span><span class="sxs-lookup"><span data-stu-id="6356c-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="6356c-425">各アプリのバージョンは、バージョン管理されたシークレット値をその`AppSecret`構成にロードし、シークレットをロードする際にバージョンを取り除く。</span><span class="sxs-lookup"><span data-stu-id="6356c-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="6356c-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタム<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="6356c-427">実装<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>は、シークレットのバージョンプレフィックスに反応して、適切なシークレットを設定にロードします。</span><span class="sxs-lookup"><span data-stu-id="6356c-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="6356c-428">`Load`名前がプレフィックスで始まると、シークレットがロードされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="6356c-429">他のシークレットは読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="6356c-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="6356c-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="6356c-430">`GetKey`:</span></span>
  * <span data-ttu-id="6356c-431">秘密名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="6356c-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="6356c-432">任意の名前の 2 つのダッシュを`KeyDelimiter`、構成で使用される区切り文字 (通常はコロン) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6356c-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="6356c-433">Azure Key Vault では、シークレット名のコロンは許可されません。</span><span class="sxs-lookup"><span data-stu-id="6356c-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="6356c-434">この`Load`メソッドは、バージョンプレフィックスを持つものを検索するために、コンテナーシークレットを反復処理するプロバイダー アルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="6356c-435">でバージョン接頭辞が見`Load`つかると、アルゴリズムは`GetKey`メソッドを使用してシークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="6356c-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="6356c-436">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むシークレット名の残りの部分を返します。</span><span class="sxs-lookup"><span data-stu-id="6356c-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="6356c-437">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="6356c-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="6356c-438">アプリのプロジェクト ファイルで指定されたアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="6356c-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="6356c-439">次の例では、アプリのバージョンが に`5.0.0.0`設定されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6356c-440">`<UserSecretsId>`プロパティがアプリのプロジェクト ファイルに存在することを確認します。 `{GUID}`</span><span class="sxs-lookup"><span data-stu-id="6356c-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="6356c-441">[シークレットマネージャツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="6356c-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="6356c-442">シークレットは、次の Azure CLI コマンドを使用して Azure キー コンテナーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="6356c-443">アプリが実行されると、キー コンテナーシークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="6356c-444">文字列のシークレット`5000-AppSecret`は、アプリのプロジェクト ファイル ( )`5.0.0.0`で指定されているアプリのバージョンと一致します。</span><span class="sxs-lookup"><span data-stu-id="6356c-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="6356c-445">`5000`バージョン (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="6356c-446">アプリ全体で、キー`AppSecret`を使用して設定を読み込むと、シークレット値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="6356c-447">アプリのバージョンがプロジェクト ファイルでに変更`5.1.0.0`され、アプリが再度実行される場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境と`5.1.0.0_secret_value_prod`運用環境です。</span><span class="sxs-lookup"><span data-stu-id="6356c-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="6356c-448">独自の<xref:Microsoft.Azure.KeyVault.KeyVaultClient>実装を<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="6356c-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="6356c-449">カスタム クライアントは、アプリ全体でクライアントの単一インスタンスを共有できます。</span><span class="sxs-lookup"><span data-stu-id="6356c-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6356c-450">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="6356c-450">Bind an array to a class</span></span>

<span data-ttu-id="6356c-451">プロバイダーは、構成値を配列に読み取って POCO 配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="6356c-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="6356c-452">キーにコロン`:`( ) 区切り記号を含める構成ソースから読み取る場合、配列を構成するキー (`:0:`, `:1:`、 &hellip; `:{n}:`) を区別するために、数値キー セグメントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="6356c-453">詳細については、「[構成 : 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6356c-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="6356c-454">Azure Key Vault キーでは、区切り記号としてコロンを使用できません。</span><span class="sxs-lookup"><span data-stu-id="6356c-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="6356c-455">このトピックで説明する方法では、階層値`--`(セクション) の区切り文字として二重ダッシュ ( ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="6356c-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="6356c-456">配列キーは、二重ダッシュと数値キー セグメント`--0--`( , )`--1--`&hellip;`--{n}--`を使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="6356c-457">JSON ファイルによって提供される次[の Serilog](https://serilog.net/)ログ プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="6356c-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="6356c-458">出力を記録する出力の出力先を`WriteTo`記述する 2 つの Serilog*シンク*を表す 2 つのオブジェクト リテラルが配列に定義されています。</span><span class="sxs-lookup"><span data-stu-id="6356c-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="6356c-459">上記の JSON ファイルに示されている構成は、ダブル ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6356c-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="6356c-460">Key</span><span class="sxs-lookup"><span data-stu-id="6356c-460">Key</span></span> | <span data-ttu-id="6356c-461">値</span><span class="sxs-lookup"><span data-stu-id="6356c-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="6356c-462">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="6356c-462">Reload secrets</span></span>

<span data-ttu-id="6356c-463">シークレットは呼び出`IConfigurationRoot.Reload()`されるまでキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6356c-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="6356c-464">キー コンテナー内の期限切れ、無効、および更新されたシークレットは、実行`Reload`されるまでアプリによっては尊重されません。</span><span class="sxs-lookup"><span data-stu-id="6356c-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="6356c-465">無効と期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="6356c-465">Disabled and expired secrets</span></span>

<span data-ttu-id="6356c-466">無効にされたシークレットと期限<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>切れのシークレットは、 .</span><span class="sxs-lookup"><span data-stu-id="6356c-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="6356c-467">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新します。</span><span class="sxs-lookup"><span data-stu-id="6356c-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6356c-468">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6356c-468">Troubleshoot</span></span>

<span data-ttu-id="6356c-469">アプリがプロバイダーを使用して構成を読み込めないと、エラー メッセージが[ASP.NET コア ログ インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6356c-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6356c-470">次の条件により、構成の読み込みができなくなります。</span><span class="sxs-lookup"><span data-stu-id="6356c-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="6356c-471">アプリまたは証明書が Azure アクティブ ディレクトリで正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="6356c-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="6356c-472">キー コンテナーが Azure キー コンテナーに存在しません。</span><span class="sxs-lookup"><span data-stu-id="6356c-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="6356c-473">アプリは、キー コンテナーにアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="6356c-474">アクセス ポリシーには、アクセス許可`Get`は`List`含みません。</span><span class="sxs-lookup"><span data-stu-id="6356c-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="6356c-475">キー コンテナーで、構成データ (名前と値のペア) の名前が正しくない、見つからない、無効にする、または期限切れです。</span><span class="sxs-lookup"><span data-stu-id="6356c-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="6356c-476">アプリのキー コンテナー名 (`KeyVaultName`)、Azure AD`AzureADApplicationId`アプリケーション ID ( )、`AzureADCertThumbprint`または Azure AD 証明書の拇印 ( ) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="6356c-477">アプリで、読み込もうとしている値のコンフィギュレーション キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="6356c-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="6356c-478">アプリのアクセス ポリシーをキー コンテナーに追加する場合、ポリシーは作成されましたが、**アクセス ポリシー** UI で **[保存]** ボタンが選択されていません。</span><span class="sxs-lookup"><span data-stu-id="6356c-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6356c-479">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6356c-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6356c-480">マイクロソフト Azure: キー コンテナー</span><span class="sxs-lookup"><span data-stu-id="6356c-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="6356c-481">マイクロソフト Azure: キー コンテナー のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6356c-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="6356c-482">Azure Key Vault の HSM で保護されたキーを生成および転送する方法</span><span class="sxs-lookup"><span data-stu-id="6356c-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="6356c-483">クラス</span><span class="sxs-lookup"><span data-stu-id="6356c-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="6356c-484">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="6356c-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="6356c-485">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="6356c-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

