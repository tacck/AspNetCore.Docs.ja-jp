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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core の構成プロバイダーの Azure Key Vault

By [Andrew Stanton-看護師](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

このドキュメントでは、 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。 ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御する。
* 構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

次のパッケージのパッケージ参照を追加します。

* [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* [Microsoft.Identity](https://www.nuget.org/packages/Azure.Identity)

## <a name="sample-app"></a>サンプル アプリ

サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。

* `Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。 マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。 `Managed`サンプルのバージョンは、Azure にデプロイする必要があります。 「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。

プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットストレージ

[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルユーザーシークレットストアから読み込まれます。

Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。 プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。

シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。 サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境のシークレットストレージ

クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。 詳細については、「」を参照してください。

1. [Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキストボックス内の検索文字列 "Azure CLI" を使用します。
   * [ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。
   * Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。

   詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。

1. まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。

1. 次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、キーコンテナーにシークレットを作成します。

   Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。 階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。 [ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。 そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。

   サンプルアプリで使用するシークレットは次のとおりです。 値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。 は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する

**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。 マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。

このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。

1. PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。 証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモしておきます。
1. PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。
1. アプリを Azure AD (**アプリの登録**) に登録します。
1. DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. [ **証明書 & シークレット**] に移動します。
   1. 公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。 *.Cer*、 *pem*、または *.crt* 証明書を使用できます。
1. Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。
1. Azure portal の **キーコンテナー** に移動します。
1. Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。
1. **[アクセス ポリシー]** を選択します。
1. **[アクセス ポリシーの追加]** を選択します。
1. [ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。
1. [ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリを展開します。

サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。

* 非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。
* 階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。 次のいずれかの方法を使用して、構成値を取得します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは、ファイルによって指定された値を使用して **Addazurekeyvault** を呼び出し *appsettings.json* ます。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

値の例:

* Key vault 名: `contosovault`
* アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。 運用環境では、値はというサフィックスで読み込まれ `_prod` ます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用する

**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。

このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。

アプリのファイルにコンテナー名を入力し *appsettings.json* ます。 このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。 アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。

サンプルアプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。 次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。 オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。

Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。

サンプルアプリ:

* クラスのインスタンスを作成し `DefaultAzureCredential` ます。資格情報は、Azure リソースの環境からアクセストークンを取得しようとします。
* インスタンスを [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) 使用して、新しいが作成され `DefaultAzureCredential` ます。
* `Azure.Security.KeyVault.Secrets.Secrets`インスタンスは、の既定の実装で使用されます。この実装では、 `Azure.Extensions.AspNetCore.Configuration.Secrets` すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

Key vault 名の値の例: `contosovault`

*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。 運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。

エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。 Azure でサービスを再起動したことを確認します。

マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。

## <a name="configuration-options"></a>構成オプション

AddAzureKeyVault は、AzureKeyVaultConfigurationOptions を受け入れることができます。

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| プロパティ         | 説明 |
| ---------------- | ----------- |
| `Manager`        | `Azure.Extensions.AspNetCore.Configuration.Secrets` シークレットの読み込みを制御するために使用されるインスタンス。 |
| `ReloadInterval` | `Timespan` キーコンテナーのポーリングによって変更が試行されるまでの待機時間。 既定値はです `null` (構成は再読み込みされません)。 |

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用する

AddAzureKeyVault は、の実装を受け入れるオーバーロードを提供します `Azure.Extensions.AspNetCore.Configuration.Secrets` 。これにより、key vault のシークレットを構成キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。 さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。

次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。 このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。 アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。 各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。

AddAzureKeyVault は、カスタムのを使用して呼び出され `Azure.Extensions.AspNetCore.Configuration.Secrets` ます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

実装によって、 `Azure.Extensions.AspNetCore.Configuration.Secrets` シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。

* `Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。 他のシークレットは読み込まれていません。
* `GetKey`:
  * シークレット名からプレフィックスを削除します。
  * 任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。 Azure Key Vault では、シークレット名にコロンを使用することはできません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。 バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクトファイルで指定されているアプリのバージョン。 次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault シークレットが読み込まれます。 の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。

1. バージョン `5000` (ダッシュ付き) は、キー名から削除されます。 アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。

1. アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。

> [!NOTE]
> <xref:Azure.Security.KeyVault.Secrets.SecretClient>AddAzureKeyVault に独自の実装を提供することもできます。 カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。

キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。 詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。 このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。 配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。

JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。 次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。

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

前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | [値] |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。 が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効なシークレットと期限切れのシークレット

無効で有効期限が切れたシークレットは、をスローし <xref:Azure.RequestFailedException> ます。 アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の状況では、構成を読み込むことができません。

* Azure Active Directory で、アプリまたは証明書が正しく構成されていません。
* キーコンテナーは Azure Key Vault に存在しません。
* アプリは、key vault にアクセスする権限がありません。
* アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。
* Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。
* アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、Azure AD 証明書の拇印 ( `AzureADCertThumbprint` )、または Azure AD directoryid () が正しくあり `AzureADDirectoryId` ません。
* 読み込みようとしている値の構成キー (名前) が正しくありません。
* アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。 ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御する。
* 構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

パッケージ参照を [Microsoft.Extensions.Configuration に追加します。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) パッケージ。

## <a name="sample-app"></a>サンプル アプリ

サンプルアプリは、 `#define` *Program.cs* ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。

* `Certificate`: Azure Key Vault クライアント ID と x.509 証明書を使用して、Azure Key Vault に格納されているシークレットにアクセスする方法を示します。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed`: [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview) を使用して、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するようにアプリを認証する方法を示します。 マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。 `Managed`サンプルのバージョンは、Azure にデプロイする必要があります。 「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources) 」セクションのガイダンスに従ってください。

プリプロセッサディレクティブ () を使用してサンプルアプリケーションを構成する方法の詳細につい `#define` ては、「」を参照してください <xref:index#preprocessor-directives-in-sample-code> 。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットストレージ

[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルユーザーシークレットストアから読み込まれます。

Secret Manager ツールでは、 `<UserSecretsId>` アプリのプロジェクトファイルにプロパティが必要です。 プロパティ値 ( `{GUID}` ) を任意の一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション) では、 `:` [ASP.NET Core 構成](xref:fundamentals/configuration/index) キー名の区切り記号として (コロン) を使用します。

シークレットマネージャーは、プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root)に開かれたコマンドシェルから使用され `{SECRET NAME}` ます。ここで、は名前、 `{SECRET VALUE}` は値です。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの [コンテンツルート](xref:fundamentals/index#content-root) からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが Azure Key Vault セクションを [含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) の Azure Key Vault に格納されている場合、 `_dev` サフィックスはに変更され `_prod` ます。 サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境のシークレットストレージ

クイックスタートに記載されている手順 [: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli) する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。 詳細については、「」を参照してください。

1. [Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキストボックス内の検索文字列 "Azure CLI" を使用します。
   * [ **Cloud Shell の起動** ] ボタンを使用して、ブラウザーで Cloud Shell を開きます。
   * Azure portal の右上隅にあるメニューの [ **Cloud Shell** ] ボタンを選択します。

   詳細については、「 [Azure Cloud Shell の](/azure/cloud-shell/overview) [Azure CLI](/cli/azure/)と概要」を参照してください。

1. まだ認証されていない場合は、コマンドを使用してサインインし `az login` ます。

1. 次のコマンドを使用してリソースグループを作成 `{RESOURCE GROUP NAME}` します。は、新しいリソースグループのリソースグループ名で、 `{LOCATION}` は Azure リージョン (datacenter) です。

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで、 `{KEY VAULT NAME}` は新しい key vault の名前、 `{LOCATION}` は Azure リージョン (datacenter) です。

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、キーコンテナーにシークレットを作成します。

   Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。 階層値 (構成セクション) `--` は、区切り記号として (2 つのダッシュ) を使用します。 [ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。 そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。

   サンプルアプリで使用するシークレットは次のとおりです。 値には、 `_prod` `_dev` 開発環境で読み込まれたサフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。 は、 `{KEY VAULT NAME}` 前の手順で作成したキーコンテナーの名前に置き換えます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する

**アプリが Azure の外部でホストされている場合** に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources) を使用することをお勧めします。 マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。

このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、アプリケーション ID と x.509 証明書を使用し `Certificate` ます。

1. PKCS # 12 アーカイブ (*.pfx*) 証明書を作成します。 証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモしておきます。
1. PKCS # 12 アーカイブ (*.pfx*) 証明書を DER でエンコードされた証明書 (*.cer*) としてエクスポートします。
1. アプリを Azure AD (**アプリの登録**) に登録します。
1. DER でエンコードされた証明書 (*.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. [ **証明書 & シークレット**] に移動します。
   1. 公開キーを含む証明書をアップロードするには、[ **証明書のアップロード** ] を選択します。 *.Cer*、 *pem*、または *.crt* 証明書を使用できます。
1. Key vault 名、アプリケーション ID、証明書の拇印をアプリのファイルに格納し *appsettings.json* ます。
1. Azure portal の **キーコンテナー** に移動します。
1. Azure Key Vault セクションで、 [運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) に作成したキーコンテナーを選択します。
1. **[アクセス ポリシー]** を選択します。
1. **[アクセス ポリシーの追加]** を選択します。
1. [ **シークレットのアクセス許可** ] を開き、アプリに **Get** および **List** アクセス許可を提供します。
1. [ **プリンシパルの選択** ] を選択し、名前で登録済みのアプリを選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリを展開します。

サンプルアプリでは、 `Certificate` `IConfigurationRoot` シークレット名と同じ名前を使用してから構成値を取得します。

* 非階層値: の値は、 `SecretName` で取得され `config["SecretName"]` ます。
* 階層値 (セクション): `:` (コロン) 表記または拡張メソッドを使用し `GetSection` ます。 次のいずれかの方法を使用して、構成値を取得します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 、ファイルによって指定された値を使用してを呼び出し *appsettings.json* ます。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

値の例:

* Key vault 名: `contosovault`
* アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値はサフィックス付きで読み込ま `_dev` れます。 運用環境では、値はというサフィックスで読み込まれ `_prod` ます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用する

**Azure にデプロイされたアプリ** は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。

このサンプルアプリでは、 `#define` *Program.cs* ファイルの先頭にあるステートメントがに設定されている場合に、Azure リソースの管理対象 id を使用し `Managed` ます。

アプリのファイルにコンテナー名を入力し *appsettings.json* ます。 このサンプルアプリでは、バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません `Managed` 。そのため、これらの構成エントリは無視してかまいません。 アプリが Azure にデプロイされ、Azure は、ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証し *appsettings.json* ます。

サンプルアプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。 次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。 オブジェクト ID は、App Service のパネルの Azure portal に表示され **Identity** ます。

Azure CLI とアプリのオブジェクト ID を使用して、 `list` `get` キーコンテナーにアクセスするためのアクセス許可とアクセス許可をアプリに付与します。

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure portal を使用して **アプリを再起動** します。

サンプルアプリ:

* `AzureServiceTokenProvider`接続文字列を指定せずに、クラスのインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンストークンのコールバックを使用して、新しいが作成され `AzureServiceTokenProvider` ます。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>インスタンスは、の既定の実装で使用されます。この実装では、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> すべてのシークレット値が読み込まれ、二重ダッシュ ( `--` ) がキー名のコロン () に置き換えられ `:` ます。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Key vault 名の値の例: `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値は `_dev` ユーザーシークレットによって提供されるため、サフィックスが付いています。 運用環境では、値は `_prod` Azure Key Vault によって提供されるため、サフィックス付きで読み込まれます。

エラーが発生した場合は `Access denied` 、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。 Azure でサービスを再起動したことを確認します。

マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「 [管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用する

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> の実装を受け入れるオーバーロードを提供します <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 ( *開発* 、 *運用* シークレットなど) を同じコンテナーに配置したりしないでください。 さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。

次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) でシークレットが確立されてい `5000-AppSecret` ます (キーコンテナーのシークレット名ではピリオドは使用できません)。 このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。 アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) シークレットを追加し `5100-AppSecret` ます。 各アプリバージョンは、バージョン管理されたシークレット値をとして構成に読み込み `AppSecret` ます。これにより、シークレットが読み込まれるときにバージョンが除去されます。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタムのを使用して呼び出され <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

実装によって、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。

* `Load` 名前がプレフィックスで始まる場合に、シークレットを読み込みます。 他のシークレットは読み込まれていません。
* `GetKey`:
  * シークレット名からプレフィックスを削除します。
  * 任意の名前の2つのダッシュを、 `KeyDelimiter` 構成で使用される区切り記号 (通常はコロン) で置き換えます。 Azure Key Vault では、シークレット名にコロンを使用することはできません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

この `Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。 バージョンプレフィックスがで見つかった場合 `Load` 、アルゴリズムは、メソッドを使用して `GetKey` シークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクトファイルで指定されているアプリのバージョン。 次の例では、アプリのバージョンがに設定されてい `5.0.0.0` ます。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`アプリケーションのプロジェクトファイルにプロパティが存在することを確認します。ここで、 `{GUID}` はユーザー指定の GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault シークレットが読み込まれます。 の文字列シークレット `5000-AppSecret` は、アプリのプロジェクトファイル () で指定されているアプリのバージョンと一致し `5.0.0.0` ます。

1. バージョン `5000` (ダッシュ付き) は、キー名から削除されます。 アプリ全体で、キーを使用して構成を読み取ると、 `AppSecret` シークレットの値が読み込まれます。

1. アプリのバージョンがプロジェクトファイルでに変更され、 `5.1.0.0` アプリが再度実行された場合、返されるシークレット値は `5.1.0.0_secret_value_dev` 開発環境および `5.1.0.0_secret_value_prod` 運用環境にあります。

> [!NOTE]
> また、に独自の実装を提供することもでき <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ます。 カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。

キーにコロン () 区切り記号を含めることができる構成ソースから読み取る場合 `:` 、配列を構成するキー (、、) を区別するために、数値キーセグメントが使用され `:0:` `:1:` &hellip; `:{n}:` ます。 詳細については、「 [構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。 このトピックで説明する方法では、 `--` 階層値 (セクション) の区切り記号として二重ダッシュ () を使用します。 配列キーは、2つのダッシュと数値のキーセグメント (、、) を使用して Azure Key Vault に格納され `--0--` `--1--` &hellip; `--{n}--` ます。

JSON ファイルによって提供される次の [Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。 次の2つのオブジェクトリテラルが配列に定義されています `WriteTo` 。この *シンク* には、ログ出力の宛先が記述されています。

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

前の JSON ファイルに示されている構成は、二重ダッシュ ( `--` ) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | [値] |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは、が呼び出されるまでキャッシュされ `IConfigurationRoot.Reload()` ます。 が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません `Reload` 。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効なシークレットと期限切れのシークレット

無効で有効期限が切れたシークレットは、をスローし <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> ます。 アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが [ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の状況では、構成を読み込むことができません。

* Azure Active Directory で、アプリまたは証明書が正しく構成されていません。
* キーコンテナーは Azure Key Vault に存在しません。
* アプリは、key vault にアクセスする権限がありません。
* アクセスポリシーには `Get` 、とのアクセス許可は含まれません `List` 。
* Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。
* アプリの key vault 名 ( `KeyVaultName` )、Azure AD アプリケーション Id ( `AzureADApplicationId` )、または Azure AD 証明書の拇印 () が正しくあり `AzureADCertThumbprint` ません。
* 読み込みようとしている値の構成キー (名前) が正しくありません。
* アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で [**保存**] ボタンが選択されていませんでした。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
