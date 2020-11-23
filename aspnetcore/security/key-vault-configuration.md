---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: rick-anderson
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: fcd5524bed11cca2380ffd8956f437f742729b55
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417613"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="cac8c-103">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cac8c-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="cac8c-104">By [Andrew Stanton-看護師](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="cac8c-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cac8c-105">このドキュメントでは、 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="cac8c-106">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="cac8c-107">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="cac8c-108">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="cac8c-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="cac8c-109">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="cac8c-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="cac8c-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cac8c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="cac8c-111">パッケージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-111">Packages</span></span>

<span data-ttu-id="cac8c-112">パッケージ参照を [Azure.Extensions.AspNetCore.Configuration に追加します。シークレット](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="cac8c-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="cac8c-113">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="cac8c-113">Sample app</span></span>

<span data-ttu-id="cac8c-114">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="cac8c-115">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="cac8c-116">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="cac8c-117">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="cac8c-118">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="cac8c-119">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="cac8c-120">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="cac8c-121">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="cac8c-122">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="cac8c-123">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cac8c-124">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="cac8c-125">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="cac8c-126">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="cac8c-127">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="cac8c-128">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="cac8c-129">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="cac8c-130">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="cac8c-131">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="cac8c-132">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="cac8c-133">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="cac8c-134">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="cac8c-135">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="cac8c-136">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="cac8c-137">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="cac8c-138">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="cac8c-139">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="cac8c-140">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="cac8c-141">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="cac8c-142">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="cac8c-143">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cac8c-144">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cac8c-145">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="cac8c-146">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="cac8c-147">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="cac8c-148">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="cac8c-149">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="cac8c-150">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="cac8c-151">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="cac8c-152">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="cac8c-153">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="cac8c-154">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="cac8c-155">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="cac8c-156">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="cac8c-157">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="cac8c-158">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="cac8c-159">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="cac8c-160">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="cac8c-161">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="cac8c-162">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="cac8c-163">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="cac8c-164">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="cac8c-165">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="cac8c-166">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="cac8c-167">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="cac8c-168">[ **証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="cac8c-169">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="cac8c-170">*.Cer*、 *pem*、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="cac8c-171">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="cac8c-172">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="cac8c-173">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="cac8c-174">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="cac8c-175">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="cac8c-176">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="cac8c-177">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="cac8c-178">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="cac8c-179">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-179">Select **OK**.</span></span>
1. <span data-ttu-id="cac8c-180">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-180">Select **Save**.</span></span>
1. <span data-ttu-id="cac8c-181">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-181">Deploy the app.</span></span>

<span data-ttu-id="cac8c-182">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="cac8c-183">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="cac8c-184">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="cac8c-185">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="cac8c-186">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="cac8c-187">アプリは、ファイルによって指定された値を使用して **Addazurekeyvault** を呼び出し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="cac8c-188">値の例:</span><span class="sxs-lookup"><span data-stu-id="cac8c-188">Example values:</span></span>

* <span data-ttu-id="cac8c-189">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cac8c-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="cac8c-190">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="cac8c-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="cac8c-191">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="cac8c-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="cac8c-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cac8c-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="cac8c-193">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cac8c-194">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="cac8c-195">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="cac8c-196">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="cac8c-197">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="cac8c-198">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="cac8c-199">アプリのファイルにコンテナー名を入力し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="cac8c-200">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="cac8c-201">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="cac8c-202">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="cac8c-203">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="cac8c-204">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="cac8c-205">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="cac8c-206">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="cac8c-207">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="cac8c-208">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="cac8c-208">The sample app:</span></span>

* <span data-ttu-id="cac8c-209">クラスのインスタンスを作成し `DefaultAzureCredential` ます。資格情報は、Azure リソースの環境からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="cac8c-210">インスタンスを [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) 使用して、新しいが作成され `DefaultAzureCredential` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="cac8c-211">`Azure.Security.KeyVault.Secrets.Secrets`インスタンスは、の既定の実装で使用されます。この実装では、 `Azure.Extensions.Aspnetcore.Configuration.Secrets` すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="cac8c-212">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cac8c-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="cac8c-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cac8c-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="cac8c-214">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cac8c-215">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="cac8c-216">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="cac8c-217">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="cac8c-218">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="cac8c-219">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="cac8c-220">構成オプション</span><span class="sxs-lookup"><span data-stu-id="cac8c-220">Configuration options</span></span>

<span data-ttu-id="cac8c-221">AddAzureKeyVault は、AzureKeyVaultConfigurationOptions を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="cac8c-222">プロパティ</span><span class="sxs-lookup"><span data-stu-id="cac8c-222">Property</span></span>         | <span data-ttu-id="cac8c-223">Description</span><span class="sxs-lookup"><span data-stu-id="cac8c-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="cac8c-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` シークレットの読み込みを制御するために使用されるインスタンス。</span><span class="sxs-lookup"><span data-stu-id="cac8c-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="cac8c-225">`Timespan` キーコンテナーのポーリングによって変更が試行されるまでの待機時間。</span><span class="sxs-lookup"><span data-stu-id="cac8c-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="cac8c-226">既定値はです `null` (構成は再読み込みされません)。</span><span class="sxs-lookup"><span data-stu-id="cac8c-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="cac8c-227">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-227">Use a key name prefix</span></span>

<span data-ttu-id="cac8c-228">AddAzureKeyVault は、の実装を受け入れるオーバーロードを提供します `Azure.Extensions.Aspnetcore.Configuration.Secrets` 。これにより、key vault のシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="cac8c-229">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="cac8c-230">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="cac8c-231">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="cac8c-232">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="cac8c-233">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="cac8c-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="cac8c-234">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="cac8c-235">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="cac8c-236">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="cac8c-237">AddAzureKeyVault は、カスタムのを使用して呼び出され `Azure.Extensions.Aspnetcore.Configuration.Secrets` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="cac8c-238">実装によって、 `Azure.Extensions.Aspnetcore.Configuration.Secrets` シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="cac8c-239">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="cac8c-240">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="cac8c-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="cac8c-241">`GetKey`:</span></span>
  * <span data-ttu-id="cac8c-242">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="cac8c-243">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="cac8c-244">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="cac8c-245">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="cac8c-246">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="cac8c-247">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="cac8c-248">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="cac8c-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="cac8c-249">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="cac8c-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="cac8c-250">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="cac8c-251">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="cac8c-252">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="cac8c-253">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="cac8c-254">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="cac8c-255">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="cac8c-256">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="cac8c-257">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="cac8c-258">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="cac8c-259"><xref:Azure.Security.KeyVault.Secrets.SecretClient>AddAzureKeyVault に独自の実装を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="cac8c-260">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cac8c-261">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="cac8c-261">Bind an array to a class</span></span>

<span data-ttu-id="cac8c-262">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="cac8c-263">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="cac8c-264">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="cac8c-265">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="cac8c-266">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="cac8c-267">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="cac8c-268">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="cac8c-269">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="cac8c-270">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="cac8c-271">キー</span><span class="sxs-lookup"><span data-stu-id="cac8c-271">Key</span></span> | <span data-ttu-id="cac8c-272">値</span><span class="sxs-lookup"><span data-stu-id="cac8c-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="cac8c-273">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="cac8c-273">Reload secrets</span></span>

<span data-ttu-id="cac8c-274">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="cac8c-275">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="cac8c-276">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="cac8c-276">Disabled and expired secrets</span></span>

<span data-ttu-id="cac8c-277">無効で有効期限が切れたシークレットは、をスローし <xref:Azure.RequestFailedException> ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="cac8c-278">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cac8c-279">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="cac8c-279">Troubleshoot</span></span>

<span data-ttu-id="cac8c-280">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cac8c-281">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="cac8c-282">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="cac8c-283">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="cac8c-284">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="cac8c-285">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="cac8c-286">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="cac8c-287">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、Azure AD 証明書の拇印 ( `AzureADCertThumbprint` )、または Azure AD directoryid () が正しくあり `AzureADDirectoryId` ません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="cac8c-288">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="cac8c-289">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="cac8c-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cac8c-290">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cac8c-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="cac8c-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="cac8c-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="cac8c-292">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="cac8c-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="cac8c-293">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="cac8c-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="cac8c-294">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="cac8c-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="cac8c-295">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="cac8c-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="cac8c-296">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="cac8c-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cac8c-297">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="cac8c-298">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="cac8c-299">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="cac8c-300">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="cac8c-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="cac8c-301">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="cac8c-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="cac8c-302">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cac8c-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="cac8c-303">パッケージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-303">Packages</span></span>

<span data-ttu-id="cac8c-304">パッケージ参照を [Microsoft.Extensions.Configuration に追加します。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="cac8c-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="cac8c-305">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="cac8c-305">Sample app</span></span>

<span data-ttu-id="cac8c-306">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="cac8c-307">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="cac8c-308">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="cac8c-309">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="cac8c-310">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="cac8c-311">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="cac8c-312">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="cac8c-313">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="cac8c-314">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="cac8c-315">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cac8c-316">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="cac8c-317">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="cac8c-318">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="cac8c-319">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="cac8c-320">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="cac8c-321">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="cac8c-322">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="cac8c-323">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="cac8c-324">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="cac8c-325">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="cac8c-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="cac8c-326">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="cac8c-327">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="cac8c-328">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="cac8c-329">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="cac8c-330">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="cac8c-331">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="cac8c-332">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="cac8c-333">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="cac8c-334">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="cac8c-335">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cac8c-336">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cac8c-337">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="cac8c-338">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="cac8c-339">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="cac8c-340">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="cac8c-341">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="cac8c-342">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="cac8c-343">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="cac8c-344">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="cac8c-345">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="cac8c-346">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="cac8c-347">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="cac8c-348">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="cac8c-349">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="cac8c-350">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="cac8c-351">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="cac8c-352">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="cac8c-353">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="cac8c-354">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="cac8c-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="cac8c-355">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="cac8c-356">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="cac8c-357">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="cac8c-358">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="cac8c-359">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="cac8c-360">[ **証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="cac8c-361">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="cac8c-362">*.Cer*、 *pem*、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="cac8c-363">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="cac8c-364">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="cac8c-365">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="cac8c-366">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="cac8c-367">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="cac8c-368">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="cac8c-369">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="cac8c-370">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="cac8c-371">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-371">Select **OK**.</span></span>
1. <span data-ttu-id="cac8c-372">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-372">Select **Save**.</span></span>
1. <span data-ttu-id="cac8c-373">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-373">Deploy the app.</span></span>

<span data-ttu-id="cac8c-374">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="cac8c-375">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="cac8c-376">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="cac8c-377">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="cac8c-378">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="cac8c-379">アプリは <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 、ファイルによって指定された値を使用してを呼び出し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="cac8c-380">値の例:</span><span class="sxs-lookup"><span data-stu-id="cac8c-380">Example values:</span></span>

* <span data-ttu-id="cac8c-381">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cac8c-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="cac8c-382">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="cac8c-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="cac8c-383">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="cac8c-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="cac8c-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cac8c-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="cac8c-385">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cac8c-386">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="cac8c-387">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="cac8c-388">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="cac8c-389">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="cac8c-390">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="cac8c-391">アプリのファイルにコンテナー名を入力し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="cac8c-392">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="cac8c-393">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="cac8c-394">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="cac8c-395">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="cac8c-396">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="cac8c-397">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="cac8c-398">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="cac8c-399">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="cac8c-400">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="cac8c-400">The sample app:</span></span>

* <span data-ttu-id="cac8c-401">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="cac8c-402">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="cac8c-403"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="cac8c-404"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="cac8c-405">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cac8c-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="cac8c-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cac8c-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="cac8c-407">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cac8c-408">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="cac8c-409">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="cac8c-410">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="cac8c-411">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="cac8c-412">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="cac8c-413">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="cac8c-413">Use a key name prefix</span></span>

<span data-ttu-id="cac8c-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="cac8c-415">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="cac8c-416">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="cac8c-417">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="cac8c-418">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cac8c-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="cac8c-419">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="cac8c-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="cac8c-420">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="cac8c-421">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="cac8c-422">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="cac8c-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="cac8c-424">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="cac8c-425">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="cac8c-426">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="cac8c-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="cac8c-427">`GetKey`:</span></span>
  * <span data-ttu-id="cac8c-428">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="cac8c-429">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="cac8c-430">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="cac8c-431">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="cac8c-432">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="cac8c-433">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="cac8c-434">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="cac8c-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="cac8c-435">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="cac8c-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="cac8c-436">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="cac8c-437">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="cac8c-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="cac8c-438">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="cac8c-439">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="cac8c-440">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="cac8c-441">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="cac8c-442">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="cac8c-443">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="cac8c-444">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="cac8c-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="cac8c-445">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="cac8c-446">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cac8c-447">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="cac8c-447">Bind an array to a class</span></span>

<span data-ttu-id="cac8c-448">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="cac8c-449">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="cac8c-450">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="cac8c-451">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="cac8c-452">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="cac8c-453">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="cac8c-454">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="cac8c-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="cac8c-455">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="cac8c-456">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="cac8c-457">キー</span><span class="sxs-lookup"><span data-stu-id="cac8c-457">Key</span></span> | <span data-ttu-id="cac8c-458">値</span><span class="sxs-lookup"><span data-stu-id="cac8c-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="cac8c-459">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="cac8c-459">Reload secrets</span></span>

<span data-ttu-id="cac8c-460">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="cac8c-461">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="cac8c-462">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="cac8c-462">Disabled and expired secrets</span></span>

<span data-ttu-id="cac8c-463">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="cac8c-464">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="cac8c-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cac8c-465">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="cac8c-465">Troubleshoot</span></span>

<span data-ttu-id="cac8c-466">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="cac8c-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cac8c-467">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="cac8c-468">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="cac8c-469">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="cac8c-470">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="cac8c-471">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="cac8c-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="cac8c-472">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="cac8c-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="cac8c-473">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="cac8c-474">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="cac8c-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="cac8c-475">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="cac8c-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cac8c-476">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cac8c-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="cac8c-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="cac8c-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="cac8c-478">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="cac8c-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="cac8c-479">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="cac8c-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="cac8c-480">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="cac8c-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="cac8c-481">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="cac8c-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="cac8c-482">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="cac8c-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
