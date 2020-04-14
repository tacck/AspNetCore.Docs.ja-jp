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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET コアの Azure キー コンテナー構成プロバイダー

[アンドリュー・スタントン=ナース](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

このドキュメントでは[、Microsoft Azure キー コンテナー](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリやサービスで使用される暗号化キーとシークレットの保護を支援するクラウドベースのサービスです。 Azure Key Vault を ASP.NET コア アプリで使用する一般的なシナリオは次のとおりです。

* 機密構成データへのアクセスを制御する。
* 構成データを格納する際に FIPS 140-2 レベル 2 の検証済みハードウェア セキュリティ モジュール (HSM) の要件を満たす。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

パッケージ参照を[追加](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)します。

## <a name="sample-app"></a>サンプル アプリ

サンプル アプリは`#define`*、Program.cs*ファイルの先頭にあるステートメントによって決まる 2 つのモードのいずれかで実行されます。

* `Certificate`&ndash; Azure Key コンテナーに格納されているシークレットにアクセスするための Azure キー コンテナー クライアント ID と X.509 証明書の使用方法を示します。 このバージョンのサンプルは、Azure App Service にデプロイされた任意の場所または ASP.NET コア アプリを提供できる任意のホストから実行できます。
* `Managed`&ndash; [Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に資格情報を格納せずに Azure AD 認証を使用して Azure Key Vault に対してアプリを認証する方法を示します。 マネージド ID を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。 サンプル`Managed`のバージョンは Azure にデプロイする必要があります。 「Azure リソースに[管理対象 ID を使用する」セクションのガイダンスに](#use-managed-identities-for-azure-resources)従います。

プリプロセッサ ディレクティブ ( )`#define`を使用してサンプル アプリを構成する<xref:index#preprocessor-directives-in-sample-code>方法の詳細については、「」を参照してください。

## <a name="secret-storage-in-the-development-environment"></a>開発環境における秘密ストレージ

[シークレットマネージャツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 サンプル アプリが開発環境のローカル コンピューターで実行されると、シークレットはローカルシークレット マネージャー ストアから読み込まれます。

シークレット マネージャー ツールでは`<UserSecretsId>`、アプリのプロジェクト ファイルにプロパティが必要です。 プロパティ値 (`{GUID}`) を一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション)`:`では[、ASP.NETコア コンフィギュレーション](xref:fundamentals/configuration/index)キー名の区切り記号としてコロンを使用します。

Secret Manager は、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に`{SECRET NAME}``{SECRET VALUE}`開かれたコマンドシェルから使用されます。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの[コンテンツ ルート](xref:fundamentals/index#content-root)からコマンド シェルで次のコマンドを実行して、サンプル アプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが [Azure Key Vault と共に運用環境] の[シークレット ストレージの Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault)に格納されている場合、`_dev`サフィックスは に`_prod`変更されます。 サフィックスは、構成値のソースを示すアプリの出力に視覚的な手掛かりを提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境でのシークレット ストレージ

[「クイック スタート: Azure CLI を使用して Azure Key Vault からシークレットを設定および取得](/azure/key-vault/quick-create-cli)する」トピックは、Azure Key Vault を作成し、サンプル アプリで使用されるシークレットを格納する方法について説明します。 詳細については、トピックを参照してください。

1. Azure ポータルで次のいずれかの方法を使用して[、Azure](https://portal.azure.com/)クラウド シェルを開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキスト ボックスの検索文字列 "Azure CLI" を使用します。
   * [クラウド シェルの起動] ボタンを使用して、ブラウザーで **[クラウド シェル**] を開きます。
   * Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。

   詳細については[、「Azure CLI」](/cli/azure/)および[「Azure クラウド シェルの概要](/azure/cloud-shell/overview)」を参照してください。

1. 認証されていない場合は、`az login`コマンドを使用してサインインします。

1. 次のコマンドでリソース グループを作成します`{RESOURCE GROUP NAME}`。 `{LOCATION}`

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、`{KEY VAULT NAME}`リソース グループにキー コンテナー`{LOCATION}`を作成します。

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. キー コンテナーに名前と値のペアとしてシークレットを作成します。

   Azure Key Vault シークレット名は、英数字とダッシュに制限されています。 階層値 (構成セクション)`--`は、区切り記号として (2 つのダッシュ) を使用します。 通常[、ASP.NETコア構成](xref:fundamentals/configuration/index)のサブキーからセクションを区切るために使用されるコロンは、キー コンテナーシークレット名では使用できません。 したがって、シークレットがアプリの構成に読み込まれると、2 つのダッシュが使用され、コロンに交換されます。

   サンプル アプリでは、次のシークレットが使用されます。 値には、`_prod`開発環境で読み込まれたユーザー`_dev`シークレットとのサフィックス値と区別するためのサフィックスが含まれます。 前`{KEY VAULT NAME}`の手順で作成したキー コンテナーの名前で置き換えます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure ホスト以外のアプリにアプリケーション ID と X.509 証明書を使用する

Azure の**外部でアプリがホストされている場合**、Azure AD、Azure キー コンテナー、およびアプリがキー コンテナーに対して認証するために Azure Active Directory アプリケーション ID と X.509 証明書を使用するようにアプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリではアプリケーション ID と X.509 証明書の使用がサポートされていますが、Azure でアプリをホストする場合は[、Azure リソースのマネージド ID](#use-managed-identities-for-azure-resources)を使用することをお勧めします。 管理対象 ID では、アプリや開発環境に証明書を格納する必要はありません。

Program.cs*ファイルの*先頭にある`Certificate``#define`ステートメントが に設定されている場合、サンプル アプリはアプリケーション ID と X.509 証明書を使用します。

1. PKCS#12 アーカイブ (*.pfx*) 証明書を作成します。 証明書を作成するためのオプションには[、Windows の MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモします。
1. PKCS#12 アーカイブ (*.pfx*) 証明書を DER エンコード証明書 (*.cer*) としてエクスポートします。
1. Azure AD (**アプリの登録) にアプリを登録**します。
1. DER エンコード証明書 (*.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. **[証明書& シークレット**] に移動します。
   1. [**証明書のアップロード**] を選択して、公開キーを含む証明書をアップロードします。 *cer* *、.pem*、または *.crt*証明書を使用できます。
1. キー コンテナー名、アプリケーション ID、および証明書の拇印をアプリの*appsettings.json*ファイルに格納します。
1. Azure ポータルの **[キー コンテナー** ] に移動します。
1. [Azure Key Vault を使用した運用環境] セクション[のシークレット ストレージで作成したキー コンテナーを](#secret-storage-in-the-production-environment-with-azure-key-vault)選択します。
1. [**アクセス ポリシー ]** を選択します。
1. [**アクセス ポリシーの追加 ]** を選択します。
1. **シークレットのアクセス許可**を開き **、Get**と**List**のアクセス許可をアプリに付与します。
1. [**プリンシパルを選択**] を選択し、登録されているアプリを名前で選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリを展開します。

サンプル`Certificate`アプリは、シークレット名`IConfigurationRoot`と同じ名前の構成値を取得します。

* 非階層値: の値`SecretName`は`config["SecretName"]`で取得されます。
* 階層値 (セクション): `:` (コロン) 表記または`GetSection`拡張メソッドを使用します。 構成値を取得するには、次のいずれかの方法を使用します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>*、appsettings.json*ファイルによって指定された値を使用して呼び出します。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

サンプル値: 

* キー コンテナー名:`contosovault`
* アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値が接尾辞とともに`_dev`ロードされます。 実稼働環境では、値に接尾辞`_prod`が読み込まれます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースにマネージド ID を使用する

**Azure にデプロイされたアプリは**[、Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を利用して、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアント シークレット) なしで Azure AD 認証を使用して Azure Key Vault で認証できます。

Program.cs*ファイルの*上部にあるステートメントが`#define`に設定されている場合、サンプル アプリは Azure リソース`Managed`のマネージド ID を使用します。

アプリの*appsettings.json*ファイルにボルト名を入力します。 サンプル アプリでは、`Managed`バージョンに設定されているアプリケーション ID とパスワード (クライアント シークレット) は必要ないので、これらの構成エントリは無視できます。 アプリは Azure にデプロイされ、Azure は*appsettings.json*ファイルに格納されているコンテナー名のみを使用して Azure Key Vault にアクセスするようにアプリを認証します。

サンプル アプリを Azure アプリ サービスにデプロイします。

Azure アプリ サービスにデプロイされたアプリは、サービスの作成時に自動的に Azure AD に登録されます。 次のコマンドで使用する展開からオブジェクト ID を取得します。 オブジェクト ID は、Azure ポータルの App Service の **[ID]** パネルに表示されます。

Azure CLI とアプリのオブジェクト ID を使用して、`list`アプリ`get`に、キー コンテナーにアクセスするためのアクセス許可とを指定します。

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure ポータルを使用して**アプリを再起動**します。

サンプル アプリ:

* 接続文字列を使用せずにクラス`AzureServiceTokenProvider`のインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースのマネージ ID からアクセス トークンを取得しようとします。
* インスタンストークン<xref:Microsoft.Azure.KeyVault.KeyVaultClient>コールバックを使用して`AzureServiceTokenProvider`新しいコードが作成されます。
* インスタンス<xref:Microsoft.Azure.KeyVault.KeyVaultClient>は、すべてのシークレット値をロード<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>し、二重ダッシュ ( ) をキー名の`--`コロン (`:`) に置き換えるデフォルトの実装で使用されます。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

キー コンテナー名の値の例:`contosovault`
    
*appsettings.json*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値にはユーザー シークレット`_dev`によって提供されるため、サフィックスが付きます。 実稼働環境では、値は Azure `_prod` Key Vault によって提供されるため、サフィックスと共に読み込まれます。

エラーが発生した`Access denied`場合は、アプリが Azure AD に登録され、キー コンテナーへのアクセスが提供されていることを確認します。 Azure でサービスを再起動したことを確認します。

マネージド ID と Azure DevOps パイプラインを持つプロバイダーの使用については、「[マネージド サービス ID を使用した VM への Azure リソース マネージャー サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)」を参照してください。

## <a name="configuration-options"></a>構成オプション

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| プロパティ         | 説明 |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>を使用して値を取得します。 |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>シークレットの読み込みを制御するために使用されるインスタンス。 |
| `ReloadInterval` | `Timespan`キー コンテナーのポーリングの試行の間に、変更を求める時間を待機します。 既定値は (`null`構成は再読み込みされません) です。 |
| `Vault`          | キー コンテナー URI。 |

## <a name="use-a-key-name-prefix"></a>キー名プレフィックスを使用する

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されており、キー コンテナーシークレットをコンフィギュレーション キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定したプレフィックス値に基づいてシークレット値を読み込むインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> 複数のアプリのシークレットを同じキー コンテナーに配置したり、環境の秘密 (*開発*と*運用*の秘密など) を同じコンテナーに配置したりするために、キー コンテナーシークレットのプレフィックスを使用しないでください。 さまざまなアプリや開発環境や開発環境では、セキュリティのレベルが高いアプリ環境を分離するために、別々のキー コンテナーを使用することをお勧めします。

次の例では、シークレットがキー コンテナーに (および開発環境用の Secret Manager ツールを使用して`5000-AppSecret`) (キー コンテナーシークレット名では許可されない) の間にシークレットが確立されます。 このシークレットは、アプリのバージョン 5.0.0.0 のアプリ シークレットを表します。 アプリの別のバージョンである 5.1.0.0 の場合、シークレットが キー コンテナーに追加されます (およびシークレット`5100-AppSecret`マネージャー ツールを使用して) の . 各アプリのバージョンは、バージョン管理されたシークレット値をその`AppSecret`構成にロードし、シークレットをロードする際にバージョンを取り除く。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタム<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>で呼び出されます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

実装<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>は、シークレットのバージョンプレフィックスに反応して、適切なシークレットを設定にロードします。

* `Load`名前がプレフィックスで始まると、シークレットがロードされます。 他のシークレットは読み込まれません。
* `GetKey`:
  * 秘密名からプレフィックスを削除します。
  * 任意の名前の 2 つのダッシュを`KeyDelimiter`、構成で使用される区切り文字 (通常はコロン) に置き換えます。 Azure Key Vault では、シークレット名のコロンは許可されません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

この`Load`メソッドは、バージョンプレフィックスを持つものを検索するために、コンテナーシークレットを反復処理するプロバイダー アルゴリズムによって呼び出されます。 でバージョン接頭辞が見`Load`つかると、アルゴリズムは`GetKey`メソッドを使用してシークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むシークレット名の残りの部分を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクト ファイルで指定されたアプリのバージョン。 次の例では、アプリのバージョンが に`5.0.0.0`設定されています。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`プロパティがアプリのプロジェクト ファイルに存在することを確認します。 `{GUID}`

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure キー コンテナーに保存されます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリが実行されると、キー コンテナーシークレットが読み込まれます。 文字列のシークレット`5000-AppSecret`は、アプリのプロジェクト ファイル ( )`5.0.0.0`で指定されているアプリのバージョンと一致します。

1. `5000`バージョン (ダッシュ付き) は、キー名から削除されます。 アプリ全体で、キー`AppSecret`を使用して設定を読み込むと、シークレット値が読み込まれます。

1. アプリのバージョンがプロジェクト ファイルでに変更`5.1.0.0`され、アプリが再度実行される場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境と`5.1.0.0_secret_value_prod`運用環境です。

> [!NOTE]
> 独自の<xref:Microsoft.Azure.KeyVault.KeyVaultClient>実装を<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>に提供することもできます。 カスタム クライアントは、アプリ全体でクライアントの単一インスタンスを共有できます。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、構成値を配列に読み取って POCO 配列にバインドできます。

キーにコロン`:`( ) 区切り記号を含める構成ソースから読み取る場合、配列を構成するキー (`:0:`, `:1:`、 &hellip; `:{n}:`) を区別するために、数値キー セグメントが使用されます。 詳細については、「[構成 : 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用できません。 このトピックで説明する方法では、階層値`--`(セクション) の区切り文字として二重ダッシュ ( ) を使用します。 配列キーは、二重ダッシュと数値キー セグメント`--0--`( , )`--1--`&hellip;`--{n}--`を使用して Azure Key Vault に格納されます。

JSON ファイルによって提供される次[の Serilog](https://serilog.net/)ログ プロバイダーの構成を確認します。 出力を記録する出力の出力先を`WriteTo`記述する 2 つの Serilog*シンク*を表す 2 つのオブジェクト リテラルが配列に定義されています。

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

上記の JSON ファイルに示されている構成は、ダブル ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | 値 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは呼び出`IConfigurationRoot.Reload()`されるまでキャッシュされます。 キー コンテナー内の期限切れ、無効、および更新されたシークレットは、実行`Reload`されるまでアプリによっては尊重されません。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効と期限切れのシークレット

無効にされたシークレットと期限<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>切れのシークレットは、 . アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新します。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用して構成を読み込めないと、エラー メッセージが[ASP.NET コア ログ インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の条件により、構成の読み込みができなくなります。

* アプリまたは証明書が Azure アクティブ ディレクトリで正しく構成されていません。
* キー コンテナーが Azure キー コンテナーに存在しません。
* アプリは、キー コンテナーにアクセスする権限がありません。
* アクセス ポリシーには、アクセス許可`Get`は`List`含みません。
* キー コンテナーで、構成データ (名前と値のペア) の名前が正しくない、見つからない、無効にする、または期限切れです。
* アプリのキー コンテナー名 (`KeyVaultName`)、Azure AD`AzureADApplicationId`アプリケーション ID ( )、`AzureADCertThumbprint`または Azure AD 証明書の拇印 ( ) が正しくありません。
* アプリで、読み込もうとしている値のコンフィギュレーション キー (名前) が正しくありません。
* アプリのアクセス ポリシーをキー コンテナーに追加する場合、ポリシーは作成されましたが、**アクセス ポリシー** UI で **[保存]** ボタンが選択されていません。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [マイクロソフト Azure: キー コンテナー](https://azure.microsoft.com/services/key-vault/)
* [マイクロソフト Azure: キー コンテナー のドキュメント](/azure/key-vault/)
* [Azure Key Vault の HSM で保護されたキーを生成および転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

このドキュメントでは[、Microsoft Azure キー コンテナー](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して、Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリやサービスで使用される暗号化キーとシークレットの保護を支援するクラウドベースのサービスです。 Azure Key Vault を ASP.NET コア アプリで使用する一般的なシナリオは次のとおりです。

* 機密構成データへのアクセスを制御する。
* 構成データを格納する際に FIPS 140-2 レベル 2 の検証済みハードウェア セキュリティ モジュール (HSM) の要件を満たす。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

パッケージ参照を[追加](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)します。

## <a name="sample-app"></a>サンプル アプリ

サンプル アプリは`#define`*、Program.cs*ファイルの先頭にあるステートメントによって決まる 2 つのモードのいずれかで実行されます。

* `Certificate`&ndash; Azure Key コンテナーに格納されているシークレットにアクセスするための Azure キー コンテナー クライアント ID と X.509 証明書の使用方法を示します。 このバージョンのサンプルは、Azure App Service にデプロイされた任意の場所または ASP.NET コア アプリを提供できる任意のホストから実行できます。
* `Managed`&ndash; [Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を使用して、アプリのコードまたは構成に資格情報を格納せずに Azure AD 認証を使用して Azure Key Vault に対してアプリを認証する方法を示します。 マネージド ID を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。 サンプル`Managed`のバージョンは Azure にデプロイする必要があります。 「Azure リソースに[管理対象 ID を使用する」セクションのガイダンスに](#use-managed-identities-for-azure-resources)従います。

プリプロセッサ ディレクティブ ( )`#define`を使用してサンプル アプリを構成する<xref:index#preprocessor-directives-in-sample-code>方法の詳細については、「」を参照してください。

## <a name="secret-storage-in-the-development-environment"></a>開発環境における秘密ストレージ

[シークレットマネージャツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 サンプル アプリが開発環境のローカル コンピューターで実行されると、シークレットはローカルシークレット マネージャー ストアから読み込まれます。

シークレット マネージャー ツールでは`<UserSecretsId>`、アプリのプロジェクト ファイルにプロパティが必要です。 プロパティ値 (`{GUID}`) を一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション)`:`では[、ASP.NETコア コンフィギュレーション](xref:fundamentals/configuration/index)キー名の区切り記号としてコロンを使用します。

Secret Manager は、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に`{SECRET NAME}``{SECRET VALUE}`開かれたコマンドシェルから使用されます。

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトの[コンテンツ ルート](xref:fundamentals/index#content-root)からコマンド シェルで次のコマンドを実行して、サンプル アプリのシークレットを設定します。

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが [Azure Key Vault と共に運用環境] の[シークレット ストレージの Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault)に格納されている場合、`_dev`サフィックスは に`_prod`変更されます。 サフィックスは、構成値のソースを示すアプリの出力に視覚的な手掛かりを提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境でのシークレット ストレージ

[「クイック スタート: Azure CLI を使用して Azure Key Vault からシークレットを設定および取得](/azure/key-vault/quick-create-cli)する」トピックは、Azure Key Vault を作成し、サンプル アプリで使用されるシークレットを格納する方法について説明します。 詳細については、トピックを参照してください。

1. Azure ポータルで次のいずれかの方法を使用して[、Azure](https://portal.azure.com/)クラウド シェルを開きます。

   * コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキスト ボックスの検索文字列 "Azure CLI" を使用します。
   * [クラウド シェルの起動] ボタンを使用して、ブラウザーで **[クラウド シェル**] を開きます。
   * Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。

   詳細については[、「Azure CLI」](/cli/azure/)および[「Azure クラウド シェルの概要](/azure/cloud-shell/overview)」を参照してください。

1. 認証されていない場合は、`az login`コマンドを使用してサインインします。

1. 次のコマンドでリソース グループを作成します`{RESOURCE GROUP NAME}`。 `{LOCATION}`

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、`{KEY VAULT NAME}`リソース グループにキー コンテナー`{LOCATION}`を作成します。

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. キー コンテナーに名前と値のペアとしてシークレットを作成します。

   Azure Key Vault シークレット名は、英数字とダッシュに制限されています。 階層値 (構成セクション)`--`は、区切り記号として (2 つのダッシュ) を使用します。 通常[、ASP.NETコア構成](xref:fundamentals/configuration/index)のサブキーからセクションを区切るために使用されるコロンは、キー コンテナーシークレット名では使用できません。 したがって、シークレットがアプリの構成に読み込まれると、2 つのダッシュが使用され、コロンに交換されます。

   サンプル アプリでは、次のシークレットが使用されます。 値には、`_prod`開発環境で読み込まれたユーザー`_dev`シークレットとのサフィックス値と区別するためのサフィックスが含まれます。 前`{KEY VAULT NAME}`の手順で作成したキー コンテナーの名前で置き換えます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure ホスト以外のアプリにアプリケーション ID と X.509 証明書を使用する

Azure の**外部でアプリがホストされている場合**、Azure AD、Azure キー コンテナー、およびアプリがキー コンテナーに対して認証するために Azure Active Directory アプリケーション ID と X.509 証明書を使用するようにアプリを構成します。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリではアプリケーション ID と X.509 証明書の使用がサポートされていますが、Azure でアプリをホストする場合は[、Azure リソースのマネージド ID](#use-managed-identities-for-azure-resources)を使用することをお勧めします。 管理対象 ID では、アプリや開発環境に証明書を格納する必要はありません。

Program.cs*ファイルの*先頭にある`Certificate``#define`ステートメントが に設定されている場合、サンプル アプリはアプリケーション ID と X.509 証明書を使用します。

1. PKCS#12 アーカイブ (*.pfx*) 証明書を作成します。 証明書を作成するためのオプションには[、Windows の MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモします。
1. PKCS#12 アーカイブ (*.pfx*) 証明書を DER エンコード証明書 (*.cer*) としてエクスポートします。
1. Azure AD (**アプリの登録) にアプリを登録**します。
1. DER エンコード証明書 (*.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. **[証明書& シークレット**] に移動します。
   1. [**証明書のアップロード**] を選択して、公開キーを含む証明書をアップロードします。 *cer* *、.pem*、または *.crt*証明書を使用できます。
1. キー コンテナー名、アプリケーション ID、および証明書の拇印をアプリの*appsettings.json*ファイルに格納します。
1. Azure ポータルの **[キー コンテナー** ] に移動します。
1. [Azure Key Vault を使用した運用環境] セクション[のシークレット ストレージで作成したキー コンテナーを](#secret-storage-in-the-production-environment-with-azure-key-vault)選択します。
1. [**アクセス ポリシー ]** を選択します。
1. [**アクセス ポリシーの追加 ]** を選択します。
1. **シークレットのアクセス許可**を開き **、Get**と**List**のアクセス許可をアプリに付与します。
1. [**プリンシパルを選択**] を選択し、登録されているアプリを名前で選択します。 **[選択]** ボタンを選択します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリを展開します。

サンプル`Certificate`アプリは、シークレット名`IConfigurationRoot`と同じ名前の構成値を取得します。

* 非階層値: の値`SecretName`は`config["SecretName"]`で取得されます。
* 階層値 (セクション): `:` (コロン) 表記または`GetSection`拡張メソッドを使用します。 構成値を取得するには、次のいずれかの方法を使用します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>*、appsettings.json*ファイルによって指定された値を使用して呼び出します。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

サンプル値: 

* キー コンテナー名:`contosovault`
* アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値が接尾辞とともに`_dev`ロードされます。 実稼働環境では、値に接尾辞`_prod`が読み込まれます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースにマネージド ID を使用する

**Azure にデプロイされたアプリは**[、Azure リソースのマネージド ID](/azure/active-directory/managed-identities-azure-resources/overview)を利用して、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアント シークレット) なしで Azure AD 認証を使用して Azure Key Vault で認証できます。

Program.cs*ファイルの*上部にあるステートメントが`#define`に設定されている場合、サンプル アプリは Azure リソース`Managed`のマネージド ID を使用します。

アプリの*appsettings.json*ファイルにボルト名を入力します。 サンプル アプリでは、`Managed`バージョンに設定されているアプリケーション ID とパスワード (クライアント シークレット) は必要ないので、これらの構成エントリは無視できます。 アプリは Azure にデプロイされ、Azure は*appsettings.json*ファイルに格納されているコンテナー名のみを使用して Azure Key Vault にアクセスするようにアプリを認証します。

サンプル アプリを Azure アプリ サービスにデプロイします。

Azure アプリ サービスにデプロイされたアプリは、サービスの作成時に自動的に Azure AD に登録されます。 次のコマンドで使用する展開からオブジェクト ID を取得します。 オブジェクト ID は、Azure ポータルの App Service の **[ID]** パネルに表示されます。

Azure CLI とアプリのオブジェクト ID を使用して、`list`アプリ`get`に、キー コンテナーにアクセスするためのアクセス許可とを指定します。

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure ポータルを使用して**アプリを再起動**します。

サンプル アプリ:

* 接続文字列を使用せずにクラス`AzureServiceTokenProvider`のインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースのマネージ ID からアクセス トークンを取得しようとします。
* インスタンストークン<xref:Microsoft.Azure.KeyVault.KeyVaultClient>コールバックを使用して`AzureServiceTokenProvider`新しいコードが作成されます。
* インスタンス<xref:Microsoft.Azure.KeyVault.KeyVaultClient>は、すべてのシークレット値をロード<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>し、二重ダッシュ ( ) をキー名の`--`コロン (`:`) に置き換えるデフォルトの実装で使用されます。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

キー コンテナー名の値の例:`contosovault`
    
*appsettings.json*

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

アプリを実行すると、Web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値にはユーザー シークレット`_dev`によって提供されるため、サフィックスが付きます。 実稼働環境では、値は Azure `_prod` Key Vault によって提供されるため、サフィックスと共に読み込まれます。

エラーが発生した`Access denied`場合は、アプリが Azure AD に登録され、キー コンテナーへのアクセスが提供されていることを確認します。 Azure でサービスを再起動したことを確認します。

マネージド ID と Azure DevOps パイプラインを持つプロバイダーの使用については、「[マネージド サービス ID を使用した VM への Azure リソース マネージャー サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)」を参照してください。

## <a name="use-a-key-name-prefix"></a>キー名プレフィックスを使用する

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されており、キー コンテナーシークレットをコンフィギュレーション キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定したプレフィックス値に基づいてシークレット値を読み込むインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> 複数のアプリのシークレットを同じキー コンテナーに配置したり、環境の秘密 (*開発*と*運用*の秘密など) を同じコンテナーに配置したりするために、キー コンテナーシークレットのプレフィックスを使用しないでください。 さまざまなアプリや開発環境や開発環境では、セキュリティのレベルが高いアプリ環境を分離するために、別々のキー コンテナーを使用することをお勧めします。

次の例では、シークレットがキー コンテナーに (および開発環境用の Secret Manager ツールを使用して`5000-AppSecret`) (キー コンテナーシークレット名では許可されない) の間にシークレットが確立されます。 このシークレットは、アプリのバージョン 5.0.0.0 のアプリ シークレットを表します。 アプリの別のバージョンである 5.1.0.0 の場合、シークレットが キー コンテナーに追加されます (およびシークレット`5100-AppSecret`マネージャー ツールを使用して) の . 各アプリのバージョンは、バージョン管理されたシークレット値をその`AppSecret`構成にロードし、シークレットをロードする際にバージョンを取り除く。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>は、カスタム<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>で呼び出されます。

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

実装<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>は、シークレットのバージョンプレフィックスに反応して、適切なシークレットを設定にロードします。

* `Load`名前がプレフィックスで始まると、シークレットがロードされます。 他のシークレットは読み込まれません。
* `GetKey`:
  * 秘密名からプレフィックスを削除します。
  * 任意の名前の 2 つのダッシュを`KeyDelimiter`、構成で使用される区切り文字 (通常はコロン) に置き換えます。 Azure Key Vault では、シークレット名のコロンは許可されません。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

この`Load`メソッドは、バージョンプレフィックスを持つものを検索するために、コンテナーシークレットを反復処理するプロバイダー アルゴリズムによって呼び出されます。 でバージョン接頭辞が見`Load`つかると、アルゴリズムは`GetKey`メソッドを使用してシークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むシークレット名の残りの部分を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクト ファイルで指定されたアプリのバージョン。 次の例では、アプリのバージョンが に`5.0.0.0`設定されています。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`プロパティがアプリのプロジェクト ファイルに存在することを確認します。 `{GUID}`

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure キー コンテナーに保存されます。

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリが実行されると、キー コンテナーシークレットが読み込まれます。 文字列のシークレット`5000-AppSecret`は、アプリのプロジェクト ファイル ( )`5.0.0.0`で指定されているアプリのバージョンと一致します。

1. `5000`バージョン (ダッシュ付き) は、キー名から削除されます。 アプリ全体で、キー`AppSecret`を使用して設定を読み込むと、シークレット値が読み込まれます。

1. アプリのバージョンがプロジェクト ファイルでに変更`5.1.0.0`され、アプリが再度実行される場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境と`5.1.0.0_secret_value_prod`運用環境です。

> [!NOTE]
> 独自の<xref:Microsoft.Azure.KeyVault.KeyVaultClient>実装を<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>に提供することもできます。 カスタム クライアントは、アプリ全体でクライアントの単一インスタンスを共有できます。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、構成値を配列に読み取って POCO 配列にバインドできます。

キーにコロン`:`( ) 区切り記号を含める構成ソースから読み取る場合、配列を構成するキー (`:0:`, `:1:`、 &hellip; `:{n}:`) を区別するために、数値キー セグメントが使用されます。 詳細については、「[構成 : 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。

Azure Key Vault キーでは、区切り記号としてコロンを使用できません。 このトピックで説明する方法では、階層値`--`(セクション) の区切り文字として二重ダッシュ ( ) を使用します。 配列キーは、二重ダッシュと数値キー セグメント`--0--`( , )`--1--`&hellip;`--{n}--`を使用して Azure Key Vault に格納されます。

JSON ファイルによって提供される次[の Serilog](https://serilog.net/)ログ プロバイダーの構成を確認します。 出力を記録する出力の出力先を`WriteTo`記述する 2 つの Serilog*シンク*を表す 2 つのオブジェクト リテラルが配列に定義されています。

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

上記の JSON ファイルに示されている構成は、ダブル ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| Key | 値 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは呼び出`IConfigurationRoot.Reload()`されるまでキャッシュされます。 キー コンテナー内の期限切れ、無効、および更新されたシークレットは、実行`Reload`されるまでアプリによっては尊重されません。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効と期限切れのシークレット

無効にされたシークレットと期限<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>切れのシークレットは、 . アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新します。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用して構成を読み込めないと、エラー メッセージが[ASP.NET コア ログ インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の条件により、構成の読み込みができなくなります。

* アプリまたは証明書が Azure アクティブ ディレクトリで正しく構成されていません。
* キー コンテナーが Azure キー コンテナーに存在しません。
* アプリは、キー コンテナーにアクセスする権限がありません。
* アクセス ポリシーには、アクセス許可`Get`は`List`含みません。
* キー コンテナーで、構成データ (名前と値のペア) の名前が正しくない、見つからない、無効にする、または期限切れです。
* アプリのキー コンテナー名 (`KeyVaultName`)、Azure AD`AzureADApplicationId`アプリケーション ID ( )、`AzureADCertThumbprint`または Azure AD 証明書の拇印 ( ) が正しくありません。
* アプリで、読み込もうとしている値のコンフィギュレーション キー (名前) が正しくありません。
* アプリのアクセス ポリシーをキー コンテナーに追加する場合、ポリシーは作成されましたが、**アクセス ポリシー** UI で **[保存]** ボタンが選択されていません。

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/configuration/index>
* [マイクロソフト Azure: キー コンテナー](https://azure.microsoft.com/services/key-vault/)
* [マイクロソフト Azure: キー コンテナー のドキュメント](/azure/key-vault/)
* [Azure Key Vault の HSM で保護されたキーを生成および転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート: .NET Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンを使用して Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

