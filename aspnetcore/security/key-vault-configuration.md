---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: rick-anderson
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/key-vault-configuration
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060132"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="2e574-103">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2e574-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="2e574-104">By [Andrew Stanton-看護師](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2e574-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e574-105">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2e574-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2e574-106">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="2e574-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2e574-107">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e574-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2e574-108">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="2e574-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2e574-109">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="2e574-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2e574-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2e574-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2e574-111">パッケージ</span><span class="sxs-lookup"><span data-stu-id="2e574-111">Packages</span></span>

<span data-ttu-id="2e574-112">パッケージ参照を [Microsoft.Extensions.Configuration に追加します。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="2e574-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2e574-113">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="2e574-113">Sample app</span></span>

<span data-ttu-id="2e574-114">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2e574-115">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2e574-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2e574-116">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2e574-117">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2e574-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2e574-118">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2e574-119">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e574-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2e574-120">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="2e574-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2e574-121">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="2e574-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2e574-122">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="2e574-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="2e574-123">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2e574-124">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="2e574-125">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="2e574-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2e574-126">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2e574-127">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2e574-128">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2e574-129">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="2e574-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2e574-130">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2e574-131">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2e574-132">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="2e574-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2e574-133">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="2e574-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2e574-134">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2e574-135">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2e574-136">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e574-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2e574-137">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2e574-138">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2e574-139">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e574-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2e574-140">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2e574-141">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2e574-142">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2e574-143">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="2e574-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2e574-144">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="2e574-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2e574-145">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2e574-146">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2e574-147">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2e574-148">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2e574-149">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="2e574-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2e574-150">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e574-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2e574-151">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2e574-152">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e574-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2e574-153">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2e574-154">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="2e574-155">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2e574-156">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e574-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2e574-157">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2e574-158">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2e574-159">PKCS # 12 アーカイブ ( *.pfx* ) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-159">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="2e574-160">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="2e574-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2e574-161">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2e574-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2e574-162">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="2e574-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2e574-163">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="2e574-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2e574-164">PKCS # 12 アーカイブ ( *.pfx* ) 証明書を DER でエンコードされた証明書 ( *.cer* ) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="2e574-164">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="2e574-165">アプリを Azure AD ( **アプリの登録** ) に登録します。</span><span class="sxs-lookup"><span data-stu-id="2e574-165">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="2e574-166">DER でエンコードされた証明書 ( *.cer* ) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="2e574-166">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="2e574-167">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2e574-168">[ **証明書 & シークレット** ] に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e574-168">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="2e574-169">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2e574-170">*.Cer* 、 *pem* 、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-170">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2e574-171">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="2e574-172">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e574-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2e574-173">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2e574-174">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-174">Select **Access policies** .</span></span>
1. <span data-ttu-id="2e574-175">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-175">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="2e574-176">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e574-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2e574-177">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2e574-178">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="2e574-179">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-179">Select **OK** .</span></span>
1. <span data-ttu-id="2e574-180">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-180">Select **Save** .</span></span>
1. <span data-ttu-id="2e574-181">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="2e574-181">Deploy the app.</span></span>

<span data-ttu-id="2e574-182">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2e574-183">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2e574-184">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2e574-185">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2e574-186">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2e574-187">アプリは <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 、ファイルによって指定された値を使用してを呼び出し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="2e574-188">値の例:</span><span class="sxs-lookup"><span data-stu-id="2e574-188">Example values:</span></span>

* <span data-ttu-id="2e574-189">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2e574-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2e574-190">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2e574-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2e574-191">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2e574-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2e574-192">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="2e574-192">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="2e574-193">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2e574-194">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="2e574-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2e574-195">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2e574-196">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2e574-197">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2e574-198">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2e574-199">アプリのファイルにコンテナー名を入力し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-199">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="2e574-200">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="2e574-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2e574-201">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="2e574-202">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="2e574-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2e574-203">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2e574-204">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2e574-205">オブジェクト ID は、App Service のパネルの Azure portal に表示され **:::no-loc(Identity):::** ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-205">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="2e574-206">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="2e574-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2e574-207">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="2e574-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2e574-208">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="2e574-208">The sample app:</span></span>

* <span data-ttu-id="2e574-209">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="2e574-210">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="2e574-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="2e574-211"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="2e574-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="2e574-213">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2e574-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="2e574-214">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="2e574-214">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2e574-215">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2e574-216">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="2e574-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2e574-217">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2e574-218">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2e574-219">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2e574-220">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="2e574-221">構成オプション</span><span class="sxs-lookup"><span data-stu-id="2e574-221">Configuration options</span></span>

<span data-ttu-id="2e574-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 次のものを受け入れることができ <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="2e574-223">プロパティ</span><span class="sxs-lookup"><span data-stu-id="2e574-223">Property</span></span>         | <span data-ttu-id="2e574-224">[説明]</span><span class="sxs-lookup"><span data-stu-id="2e574-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="2e574-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> 値を取得するために使用する。</span><span class="sxs-lookup"><span data-stu-id="2e574-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="2e574-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットの読み込みを制御するために使用されるインスタンス。</span><span class="sxs-lookup"><span data-stu-id="2e574-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="2e574-227">`Timespan` キーコンテナーのポーリングによって変更が試行されるまでの待機時間。</span><span class="sxs-lookup"><span data-stu-id="2e574-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="2e574-228">既定値はです `null` (構成は再読み込みされません)。</span><span class="sxs-lookup"><span data-stu-id="2e574-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="2e574-229">Key vault URI。</span><span class="sxs-lookup"><span data-stu-id="2e574-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2e574-230">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-230">Use a key name prefix</span></span>

<span data-ttu-id="2e574-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2e574-232">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2e574-233">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2e574-234">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="2e574-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2e574-235">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e574-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2e574-236">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="2e574-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2e574-237">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="2e574-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2e574-238">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2e574-239">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2e574-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2e574-241">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2e574-242">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2e574-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2e574-243">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2e574-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2e574-244">`GetKey`:</span></span>
  * <span data-ttu-id="2e574-245">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="2e574-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2e574-246">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e574-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2e574-247">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2e574-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2e574-248">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2e574-249">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="2e574-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2e574-250">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="2e574-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2e574-251">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="2e574-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="2e574-252">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="2e574-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2e574-253">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2e574-254">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="2e574-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2e574-255">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="2e574-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2e574-256">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2e574-257">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2e574-258">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2e574-259">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2e574-260">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2e574-261">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="2e574-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2e574-262">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="2e574-263">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="2e574-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2e574-264">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="2e574-264">Bind an array to a class</span></span>

<span data-ttu-id="2e574-265">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2e574-266">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2e574-267">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2e574-268">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2e574-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2e574-269">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2e574-270">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2e574-271">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2e574-272">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="2e574-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="2e574-273">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2e574-274">キー</span><span class="sxs-lookup"><span data-stu-id="2e574-274">Key</span></span> | <span data-ttu-id="2e574-275">値</span><span class="sxs-lookup"><span data-stu-id="2e574-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2e574-276">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="2e574-276">Reload secrets</span></span>

<span data-ttu-id="2e574-277">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2e574-278">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="2e574-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2e574-279">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="2e574-279">Disabled and expired secrets</span></span>

<span data-ttu-id="2e574-280">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="2e574-281">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2e574-282">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2e574-282">Troubleshoot</span></span>

<span data-ttu-id="2e574-283">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2e574-284">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="2e574-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2e574-285">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2e574-286">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="2e574-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2e574-287">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2e574-288">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="2e574-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2e574-289">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="2e574-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2e574-290">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="2e574-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="2e574-291">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2e574-292">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、 **アクセスポリシー** UI で [ **保存** ] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="2e574-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e574-293">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2e574-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2e574-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="2e574-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2e574-295">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2e574-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2e574-296">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="2e574-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2e574-297">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="2e574-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2e574-298">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="2e574-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2e574-299">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="2e574-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e574-300">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2e574-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2e574-301">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="2e574-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2e574-302">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e574-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2e574-303">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="2e574-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2e574-304">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="2e574-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2e574-305">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2e574-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2e574-306">パッケージ</span><span class="sxs-lookup"><span data-stu-id="2e574-306">Packages</span></span>

<span data-ttu-id="2e574-307">パッケージ参照を [Microsoft.Extensions.Configuration に追加します。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) パッケージ。</span><span class="sxs-lookup"><span data-stu-id="2e574-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2e574-308">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="2e574-308">Sample app</span></span>

<span data-ttu-id="2e574-309">サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2e574-310">`Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2e574-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2e574-311">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2e574-312">`Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2e574-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2e574-313">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2e574-314">`Managed`サンプルのバージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e574-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2e574-315">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="2e574-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2e574-316">プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="2e574-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2e574-317">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="2e574-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="2e574-318">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2e574-319">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="2e574-320">Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="2e574-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2e574-321">プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2e574-322">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2e574-323">階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2e574-324">シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。</span><span class="sxs-lookup"><span data-stu-id="2e574-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2e574-325">プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="2e574-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2e574-326">これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2e574-327">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="2e574-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2e574-328">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="2e574-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2e574-329">クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2e574-330">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2e574-331">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e574-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2e574-332">コード ブロックの右上隅にある **[使ってみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2e574-333">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2e574-334">[ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e574-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2e574-335">Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2e574-336">詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2e574-337">まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2e574-338">次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="2e574-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2e574-339">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="2e574-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2e574-340">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2e574-341">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2e574-342">階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2e574-343">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2e574-344">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="2e574-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2e574-345">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e574-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2e574-346">値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2e574-347">は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e574-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2e574-348">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2e574-349">**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="2e574-350">詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2e574-351">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e574-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2e574-352">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2e574-353">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2e574-354">PKCS # 12 アーカイブ ( *.pfx* ) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-354">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="2e574-355">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="2e574-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2e574-356">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2e574-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2e574-357">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="2e574-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2e574-358">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="2e574-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2e574-359">PKCS # 12 アーカイブ ( *.pfx* ) 証明書を DER でエンコードされた証明書 ( *.cer* ) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="2e574-359">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="2e574-360">アプリを Azure AD ( **アプリの登録** ) に登録します。</span><span class="sxs-lookup"><span data-stu-id="2e574-360">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="2e574-361">DER でエンコードされた証明書 ( *.cer* ) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="2e574-361">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="2e574-362">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2e574-363">[ **証明書 & シークレット** ] に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e574-363">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="2e574-364">公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2e574-365">*.Cer* 、 *pem* 、または *.crt* 証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-365">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2e574-366">Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="2e574-367">Azure portal の **キーコンテナー** に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e574-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2e574-368">Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2e574-369">**[アクセス ポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-369">Select **Access policies** .</span></span>
1. <span data-ttu-id="2e574-370">**[アクセス ポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-370">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="2e574-371">[ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e574-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2e574-372">[ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2e574-373">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="2e574-374">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-374">Select **OK** .</span></span>
1. <span data-ttu-id="2e574-375">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e574-375">Select **Save** .</span></span>
1. <span data-ttu-id="2e574-376">アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="2e574-376">Deploy the app.</span></span>

<span data-ttu-id="2e574-377">サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2e574-378">非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2e574-379">階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2e574-380">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2e574-381">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2e574-382">アプリは <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 、ファイルによって指定された値を使用してを呼び出し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="2e574-383">値の例:</span><span class="sxs-lookup"><span data-stu-id="2e574-383">Example values:</span></span>

* <span data-ttu-id="2e574-384">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2e574-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2e574-385">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2e574-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2e574-386">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2e574-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2e574-387">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="2e574-387">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="2e574-388">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2e574-389">開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。</span><span class="sxs-lookup"><span data-stu-id="2e574-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2e574-390">運用環境では、値はというサフィックスで読み込まれ `_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2e574-391">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2e574-392">**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2e574-393">このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2e574-394">アプリのファイルにコンテナー名を入力し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-394">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="2e574-395">このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="2e574-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2e574-396">アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *:::no-loc(appsettings.json):::* ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="2e574-397">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="2e574-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2e574-398">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2e574-399">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="2e574-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2e574-400">オブジェクト ID は、App Service のパネルの Azure portal に表示され **:::no-loc(Identity):::** ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-400">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="2e574-401">Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="2e574-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2e574-402">Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。</span><span class="sxs-lookup"><span data-stu-id="2e574-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2e574-403">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="2e574-403">The sample app:</span></span>

* <span data-ttu-id="2e574-404">`AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e574-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="2e574-405">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="2e574-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="2e574-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="2e574-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="2e574-408">Key vault 名の値の例: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="2e574-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="2e574-409">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="2e574-409">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2e574-410">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2e574-411">開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="2e574-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2e574-412">運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2e574-413">エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2e574-414">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2e574-415">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2e574-416">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="2e574-416">Use a key name prefix</span></span>

<span data-ttu-id="2e574-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2e574-418">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="2e574-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2e574-419">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2e574-420">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="2e574-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2e574-421">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e574-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2e574-422">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。</span><span class="sxs-lookup"><span data-stu-id="2e574-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2e574-423">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="2e574-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2e574-424">アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2e574-425">各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2e574-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2e574-427">実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2e574-428">`Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2e574-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2e574-429">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2e574-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2e574-430">`GetKey`:</span></span>
  * <span data-ttu-id="2e574-431">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="2e574-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2e574-432">任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e574-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2e574-433">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2e574-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2e574-434">この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2e574-435">バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="2e574-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2e574-436">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="2e574-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2e574-437">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="2e574-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="2e574-438">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="2e574-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2e574-439">次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2e574-440">`<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="2e574-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2e574-441">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="2e574-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2e574-442">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2e574-443">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2e574-444">の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2e574-445">バージョン `5000` (ダッシュ付き) は、キー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2e574-446">アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2e574-447">アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。</span><span class="sxs-lookup"><span data-stu-id="2e574-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2e574-448">また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="2e574-449">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="2e574-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2e574-450">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="2e574-450">Bind an array to a class</span></span>

<span data-ttu-id="2e574-451">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="2e574-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2e574-452">キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2e574-453">詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2e574-454">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2e574-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2e574-455">このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e574-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2e574-456">配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2e574-457">JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="2e574-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2e574-458">次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。</span><span class="sxs-lookup"><span data-stu-id="2e574-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

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

<span data-ttu-id="2e574-459">前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="2e574-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2e574-460">キー</span><span class="sxs-lookup"><span data-stu-id="2e574-460">Key</span></span> | <span data-ttu-id="2e574-461">値</span><span class="sxs-lookup"><span data-stu-id="2e574-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2e574-462">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="2e574-462">Reload secrets</span></span>

<span data-ttu-id="2e574-463">シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2e574-464">が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="2e574-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2e574-465">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="2e574-465">Disabled and expired secrets</span></span>

<span data-ttu-id="2e574-466">無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。</span><span class="sxs-lookup"><span data-stu-id="2e574-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="2e574-467">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="2e574-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2e574-468">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2e574-468">Troubleshoot</span></span>

<span data-ttu-id="2e574-469">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2e574-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2e574-470">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="2e574-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2e574-471">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="2e574-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2e574-472">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="2e574-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2e574-473">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2e574-474">アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。</span><span class="sxs-lookup"><span data-stu-id="2e574-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2e574-475">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="2e574-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2e574-476">アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。</span><span class="sxs-lookup"><span data-stu-id="2e574-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="2e574-477">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="2e574-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2e574-478">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、 **アクセスポリシー** UI で [ **保存** ] ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="2e574-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e574-479">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2e574-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2e574-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="2e574-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2e574-481">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2e574-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2e574-482">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="2e574-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2e574-483">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="2e574-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2e574-484">クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う</span><span class="sxs-lookup"><span data-stu-id="2e574-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2e574-485">チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="2e574-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

