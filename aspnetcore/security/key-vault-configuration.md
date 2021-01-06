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
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792081"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="97e56-103">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="97e56-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="97e56-104">By [Andrew Stanton-看護師](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="97e56-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97e56-105">このドキュメントでは、 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97e56-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="97e56-106">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="97e56-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="97e56-107">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97e56-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="97e56-108">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="97e56-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="97e56-109">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="97e56-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="97e56-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="97e56-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="97e56-111">パッケージ</span><span class="sxs-lookup"><span data-stu-id="97e56-111">Packages</span></span>

<span data-ttu-id="97e56-112">次のパッケージのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="97e56-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="97e56-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="97e56-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="97e56-114">[Microsoft.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="97e56-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="97e56-115">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="97e56-115">Sample app</span></span>

<span data-ttu-id="97e56-116">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="97e56-117">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="97e56-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="97e56-118">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="97e56-119">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="97e56-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="97e56-120">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="97e56-121">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="97e56-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="97e56-122">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="97e56-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="97e56-123">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="97e56-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="97e56-124">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="97e56-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="97e56-125">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="97e56-126">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルユーザーシークレットストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="97e56-127">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="97e56-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="97e56-128">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="97e56-129">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="97e56-130">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="97e56-131">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="97e56-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="97e56-132">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="97e56-133">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="97e56-134">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="97e56-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="97e56-135">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="97e56-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="97e56-136">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="97e56-137">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="97e56-138">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="97e56-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="97e56-139">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="97e56-140">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="97e56-141">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="97e56-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="97e56-142">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="97e56-143">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="97e56-144">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="97e56-145">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="97e56-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="97e56-146">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="97e56-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="97e56-147">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="97e56-148">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="97e56-149">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="97e56-150">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="97e56-151">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="97e56-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="97e56-152">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97e56-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="97e56-153">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="97e56-154">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97e56-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="97e56-155">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="97e56-156">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="97e56-157">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="97e56-158">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="97e56-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="97e56-159">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="97e56-160">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="97e56-161">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="97e56-162">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="97e56-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="97e56-163">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="97e56-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="97e56-164">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="97e56-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="97e56-165">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="97e56-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="97e56-166">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="97e56-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="97e56-167">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="97e56-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="97e56-168">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="97e56-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="97e56-169">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="97e56-170">[ **証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="97e56-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="97e56-171">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="97e56-172">*.Cer*、 *pem*、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="97e56-173">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="97e56-174">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="97e56-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="97e56-175">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="97e56-176">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="97e56-177">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="97e56-178">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="97e56-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="97e56-179">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="97e56-180">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="97e56-181">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-181">Select **OK**.</span></span>
1. <span data-ttu-id="97e56-182">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-182">Select **Save**.</span></span>
1. <span data-ttu-id="97e56-183">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="97e56-183">Deploy the app.</span></span>

<span data-ttu-id="97e56-184">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="97e56-185">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="97e56-186">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="97e56-187">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="97e56-188">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="97e56-189">アプリは、ファイルによって指定された値を使用して **Addazurekeyvault** を呼び出し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="97e56-190">値の例:</span><span class="sxs-lookup"><span data-stu-id="97e56-190">Example values:</span></span>

* <span data-ttu-id="97e56-191">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="97e56-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="97e56-192">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="97e56-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="97e56-193">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="97e56-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="97e56-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97e56-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="97e56-195">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="97e56-196">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="97e56-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="97e56-197">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="97e56-198">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="97e56-199">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="97e56-200">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="97e56-201">アプリのファイルにコンテナー名を入力し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="97e56-202">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="97e56-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="97e56-203">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="97e56-204">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="97e56-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="97e56-205">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="97e56-206">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="97e56-207">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="97e56-208">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="97e56-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="97e56-209">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="97e56-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="97e56-210">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="97e56-210">The sample app:</span></span>

* <span data-ttu-id="97e56-211">クラスのインスタンスを作成し `DefaultAzureCredential` ます。資格情報は、Azure リソースの環境からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="97e56-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="97e56-212">インスタンスを [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) 使用して、新しいが作成され `DefaultAzureCredential` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="97e56-213">`Azure.Security.KeyVault.Secrets.Secrets`インスタンスは、の既定の実装で使用されます。この実装では、 `Azure.Extensions.AspNetCore.Configuration.Secrets` すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="97e56-214">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="97e56-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="97e56-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97e56-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="97e56-216">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="97e56-217">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="97e56-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="97e56-218">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="97e56-219">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="97e56-220">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="97e56-221">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="97e56-222">構成オプション</span><span class="sxs-lookup"><span data-stu-id="97e56-222">Configuration options</span></span>

<span data-ttu-id="97e56-223">AddAzureKeyVault は、AzureKeyVaultConfigurationOptions を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="97e56-224">プロパティ</span><span class="sxs-lookup"><span data-stu-id="97e56-224">Property</span></span>         | <span data-ttu-id="97e56-225">説明</span><span class="sxs-lookup"><span data-stu-id="97e56-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="97e56-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` シークレットの読み込みを制御するために使用されるインスタンス。</span><span class="sxs-lookup"><span data-stu-id="97e56-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="97e56-227">`Timespan` キーコンテナーのポーリングによって変更が試行されるまでの待機時間。</span><span class="sxs-lookup"><span data-stu-id="97e56-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="97e56-228">既定値はです `null` (構成は再読み込みされません)。</span><span class="sxs-lookup"><span data-stu-id="97e56-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="97e56-229">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-229">Use a key name prefix</span></span>

<span data-ttu-id="97e56-230">AddAzureKeyVault は、の実装を受け入れるオーバーロードを提供します `Azure.Extensions.AspNetCore.Configuration.Secrets` 。これにより、key vault のシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="97e56-231">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="97e56-232">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="97e56-233">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="97e56-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="97e56-234">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="97e56-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="97e56-235">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="97e56-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="97e56-236">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="97e56-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="97e56-237">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="97e56-238">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="97e56-239">AddAzureKeyVault は、カスタムのを使用して呼び出され `Azure.Extensions.AspNetCore.Configuration.Secrets` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="97e56-240">実装によって、 `Azure.Extensions.AspNetCore.Configuration.Secrets` シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="97e56-241">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="97e56-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="97e56-242">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="97e56-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="97e56-243">`GetKey`:</span></span>
  * <span data-ttu-id="97e56-244">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="97e56-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="97e56-245">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97e56-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="97e56-246">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="97e56-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="97e56-247">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="97e56-248">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="97e56-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="97e56-249">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="97e56-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="97e56-250">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="97e56-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="97e56-251">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="97e56-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="97e56-252">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="97e56-253">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="97e56-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="97e56-254">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="97e56-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="97e56-255">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="97e56-256">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="97e56-257">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="97e56-258">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="97e56-259">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="97e56-260">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="97e56-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="97e56-261"><xref:Azure.Security.KeyVault.Secrets.SecretClient>AddAzureKeyVault に独自の実装を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="97e56-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="97e56-262">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="97e56-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="97e56-263">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="97e56-263">Bind an array to a class</span></span>

<span data-ttu-id="97e56-264">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="97e56-265">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="97e56-266">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="97e56-267">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="97e56-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="97e56-268">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="97e56-269">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="97e56-270">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="97e56-271">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="97e56-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="97e56-272">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="97e56-273">Key</span><span class="sxs-lookup"><span data-stu-id="97e56-273">Key</span></span> | <span data-ttu-id="97e56-274">[値]</span><span class="sxs-lookup"><span data-stu-id="97e56-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="97e56-275">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="97e56-275">Reload secrets</span></span>

<span data-ttu-id="97e56-276">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="97e56-277">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="97e56-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="97e56-278">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="97e56-278">Disabled and expired secrets</span></span>

<span data-ttu-id="97e56-279">無効で有効期限が切れたシークレットは、をスローし <xref:Azure.RequestFailedException> ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="97e56-280">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="97e56-281">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="97e56-281">Troubleshoot</span></span>

<span data-ttu-id="97e56-282">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="97e56-283">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="97e56-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="97e56-284">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="97e56-285">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="97e56-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="97e56-286">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="97e56-287">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="97e56-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="97e56-288">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="97e56-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="97e56-289">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、Azure AD 証明書の拇印 ( `AzureADCertThumbprint` )、または Azure AD directoryid () が正しくあり `AzureADDirectoryId` ません。</span><span class="sxs-lookup"><span data-stu-id="97e56-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="97e56-290">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="97e56-291">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="97e56-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97e56-292">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="97e56-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="97e56-293">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="97e56-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="97e56-294">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="97e56-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="97e56-295">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="97e56-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="97e56-296">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="97e56-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="97e56-297">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="97e56-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="97e56-298">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="97e56-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="97e56-299">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97e56-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="97e56-300">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="97e56-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="97e56-301">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97e56-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="97e56-302">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="97e56-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="97e56-303">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="97e56-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="97e56-304">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="97e56-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="97e56-305">パッケージ</span><span class="sxs-lookup"><span data-stu-id="97e56-305">Packages</span></span>

<span data-ttu-id="97e56-306">パッケージ参照を [Microsoft.Extensions.Configuration に追加します。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="97e56-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="97e56-307">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="97e56-307">Sample app</span></span>

<span data-ttu-id="97e56-308">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="97e56-309">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="97e56-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="97e56-310">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="97e56-311">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="97e56-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="97e56-312">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="97e56-313">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="97e56-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="97e56-314">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="97e56-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="97e56-315">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="97e56-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="97e56-316">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="97e56-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="97e56-317">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="97e56-318">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルユーザーシークレットストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="97e56-319">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="97e56-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="97e56-320">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="97e56-321">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="97e56-322">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="97e56-323">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="97e56-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="97e56-324">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="97e56-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="97e56-325">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="97e56-326">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="97e56-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="97e56-327">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="97e56-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="97e56-328">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="97e56-329">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="97e56-330">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="97e56-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="97e56-331">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="97e56-332">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="97e56-333">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="97e56-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="97e56-334">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="97e56-335">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="97e56-336">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="97e56-337">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="97e56-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="97e56-338">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="97e56-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="97e56-339">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="97e56-340">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="97e56-341">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="97e56-342">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="97e56-343">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="97e56-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="97e56-344">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97e56-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="97e56-345">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="97e56-346">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97e56-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="97e56-347">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="97e56-348">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="97e56-349">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="97e56-350">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="97e56-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="97e56-351">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="97e56-352">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="97e56-353">PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="97e56-354">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="97e56-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="97e56-355">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="97e56-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="97e56-356">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="97e56-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="97e56-357">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="97e56-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="97e56-358">PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="97e56-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="97e56-359">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="97e56-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="97e56-360">DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="97e56-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="97e56-361">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="97e56-362">[ **証明書 & シークレット**] に移動します。</span><span class="sxs-lookup"><span data-stu-id="97e56-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="97e56-363">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="97e56-364">*.Cer*、 *pem*、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="97e56-365">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="97e56-366">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="97e56-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="97e56-367">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="97e56-368">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="97e56-369">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="97e56-370">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="97e56-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="97e56-371">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="97e56-372">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="97e56-373">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-373">Select **OK**.</span></span>
1. <span data-ttu-id="97e56-374">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97e56-374">Select **Save**.</span></span>
1. <span data-ttu-id="97e56-375">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="97e56-375">Deploy the app.</span></span>

<span data-ttu-id="97e56-376">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="97e56-377">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="97e56-378">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="97e56-379">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="97e56-380">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="97e56-381">アプリは <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 、ファイルによって指定された値を使用してを呼び出し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="97e56-382">値の例:</span><span class="sxs-lookup"><span data-stu-id="97e56-382">Example values:</span></span>

* <span data-ttu-id="97e56-383">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="97e56-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="97e56-384">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="97e56-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="97e56-385">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="97e56-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="97e56-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97e56-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="97e56-387">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="97e56-388">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="97e56-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="97e56-389">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="97e56-390">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="97e56-391">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="97e56-392">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="97e56-393">アプリのファイルにコンテナー名を入力し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="97e56-394">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="97e56-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="97e56-395">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="97e56-396">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="97e56-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="97e56-397">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="97e56-398">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="97e56-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="97e56-399">オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="97e56-400">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="97e56-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="97e56-401">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="97e56-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="97e56-402">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="97e56-402">The sample app:</span></span>

* <span data-ttu-id="97e56-403">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="97e56-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="97e56-404">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="97e56-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="97e56-405"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="97e56-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="97e56-407">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="97e56-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="97e56-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97e56-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="97e56-409">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="97e56-410">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="97e56-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="97e56-411">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="97e56-412">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="97e56-413">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="97e56-414">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="97e56-415">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="97e56-415">Use a key name prefix</span></span>

<span data-ttu-id="97e56-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="97e56-417">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="97e56-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="97e56-418">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="97e56-419">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="97e56-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="97e56-420">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="97e56-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="97e56-421">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="97e56-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="97e56-422">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="97e56-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="97e56-423">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="97e56-424">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="97e56-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="97e56-426">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="97e56-427">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="97e56-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="97e56-428">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="97e56-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="97e56-429">`GetKey`:</span></span>
  * <span data-ttu-id="97e56-430">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="97e56-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="97e56-431">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97e56-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="97e56-432">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="97e56-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="97e56-433">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="97e56-434">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="97e56-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="97e56-435">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="97e56-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="97e56-436">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="97e56-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="97e56-437">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="97e56-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="97e56-438">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="97e56-439">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="97e56-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="97e56-440">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="97e56-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="97e56-441">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="97e56-442">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="97e56-443">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="97e56-444">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="97e56-445">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="97e56-446">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="97e56-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="97e56-447">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="97e56-448">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="97e56-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="97e56-449">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="97e56-449">Bind an array to a class</span></span>

<span data-ttu-id="97e56-450">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="97e56-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="97e56-451">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="97e56-452">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="97e56-453">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="97e56-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="97e56-454">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="97e56-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="97e56-455">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="97e56-456">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="97e56-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="97e56-457">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="97e56-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="97e56-458">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="97e56-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="97e56-459">Key</span><span class="sxs-lookup"><span data-stu-id="97e56-459">Key</span></span> | <span data-ttu-id="97e56-460">[値]</span><span class="sxs-lookup"><span data-stu-id="97e56-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="97e56-461">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="97e56-461">Reload secrets</span></span>

<span data-ttu-id="97e56-462">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="97e56-463">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="97e56-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="97e56-464">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="97e56-464">Disabled and expired secrets</span></span>

<span data-ttu-id="97e56-465">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="97e56-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="97e56-466">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="97e56-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="97e56-467">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="97e56-467">Troubleshoot</span></span>

<span data-ttu-id="97e56-468">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="97e56-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="97e56-469">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="97e56-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="97e56-470">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="97e56-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="97e56-471">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="97e56-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="97e56-472">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="97e56-473">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="97e56-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="97e56-474">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="97e56-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="97e56-475">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="97e56-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="97e56-476">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="97e56-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="97e56-477">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="97e56-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97e56-478">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="97e56-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="97e56-479">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="97e56-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="97e56-480">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="97e56-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="97e56-481">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="97e56-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="97e56-482">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="97e56-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="97e56-483">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="97e56-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="97e56-484">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="97e56-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
