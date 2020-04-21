---
title: ASP.NETコアの開発におけるアプリの秘密の安全な保管
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして保存および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661799"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NETコアの開発におけるアプリの秘密の安全な保管

::: moniker range=">= aspnetcore-3.0"

[リック・アンダーソン](https://twitter.com/RickAndMSFT) [、カーク・ラーキン](https://twitter.com/serpent5)、[ダニエル・ロス](https://github.com/danroth27)、[スコット・アディ](https://github.com/scottaddie)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このドキュメントでは、開発マシン上の ASP.NET Core アプリの開発中に機密データを格納および取得する方法について説明します。 パスワードやその他の機密データをソース コードに保存しないでください。 生産の秘密は、開発やテストに使用しないでください。 シークレットはアプリと共に展開しないでください。 代わりに、シークレットは、環境変数、Azure Key Vault などの制御された手段を使用して運用環境で使用できるようにする必要があります。Azure [Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを保存および保護できます。

## <a name="environment-variables"></a>環境変数

環境変数は、コードまたはローカル構成ファイルにアプリ シークレットが格納されないようにするために使用されます。 環境変数は、以前に指定したすべての構成ソースの構成値をオーバーライドします。

**個々のユーザー アカウント**のセキュリティが有効になっているASP.NETコア Web アプリを検討してください。 既定のデータベース接続文字列は、プロジェクトの*appsettings.json*ファイルにキー`DefaultConnection`を付けて含まれています。 既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB 用です。 アプリの展開中に`DefaultConnection`、キー値を環境変数の値でオーバーライドできます。 環境変数には、機密性の高い資格情報を含む完全な接続文字列が格納される場合があります。

> [!WARNING]
> 環境変数は、通常、暗号化されていないプレーン テキストに格納されます。 マシンまたはプロセスが侵害された場合、環境変数は信頼されていないパーティからアクセスできます。 ユーザーの秘密の漏えいを防ぐための追加措置が必要な場合があります。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>シークレットマネージャー

シークレット マネージャー ツールは、ASP.NET コア プロジェクトの開発中に機密データを格納します。 このコンテキストでは、機密データの一部はアプリの秘密です。 アプリ シークレットは、プロジェクト ツリーとは別の場所に格納されます。 アプリ シークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクトで共有されます。 アプリシークレットはソース管理にチェックインされません。

> [!WARNING]
> シークレット マネージャー ツールは、保存されているシークレットを暗号化しないので、信頼できるストアとして扱う必要はありません。 開発目的のみのためです。 キーと値は、ユーザー プロファイル ディレクトリの JSON 設定ファイルに格納されます。

## <a name="how-the-secret-manager-tool-works"></a>シークレットマネージャーツールの仕組み

シークレット マネージャー ツールは、値が格納される場所や方法など、実装の詳細を抽象化します。 これらの実装の詳細を知らなくても、このツールを使用できます。 値は、ローカル コンピューター上のシステムで保護されたユーザー プロファイル フォルダーに JSON 構成ファイルに格納されます。

# <a name="windows"></a>[Windows](#tab/windows)

ファイル システム パス:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

ファイル システム パス:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

上記のファイル パスで`<user_secrets_id>`*、.csproj*ファイルで指定された`UserSecretsId`値に置き換えます。

シークレット マネージャー ツールで保存されたデータの場所や形式に依存するコードを記述しないでください。 これらの実装の詳細は変更される場合があります。 たとえば、シークレット値は暗号化されませんが、将来の値になる可能性があります。

## <a name="enable-secret-storage"></a>シークレット ストレージを有効にする

シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクト固有の構成設定に対して動作します。

シークレット マネージャー ツールには`init`、.NET Core SDK 3.0.100 以降のコマンドが含まれています。 ユーザー シークレットを使用するには、プロジェクト ディレクトリで次のコマンドを実行します。

```dotnetcli
dotnet user-secrets init
```

上記のコマンドは`UserSecretsId`*、.csproj*ファイル`PropertyGroup`内に要素を追加します。 既定では、 の`UserSecretsId`内部テキストは GUID です。 内部テキストは任意ですが、プロジェクトに固有です。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio で、ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューから **[ユーザー シークレットの管理**] を選択します。 このジェスチャは`UserSecretsId`*、.csproj*ファイルに GUID を設定した要素を追加します。

## <a name="set-a-secret"></a>秘密を設定する

キーとその値で構成されるアプリ シークレットを定義します。 シークレットはプロジェクトの`UserSecretsId`値に関連付けられます。 たとえば *、.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

上の例では、コロンはプロパティを`Movies`持つオブジェクト リテラルを`ServiceApiKey`示しています。

シークレットマネージャツールは、他のディレクトリからも使用できます。 `--project` *.csproj*ファイルが存在するファイル システム パスを指定するには、このオプションを使用します。 次に例を示します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>ビジュアル スタジオでの JSON 構造のフラット化

Visual Studio の**ユーザー シークレットの管理**ジェスチャは、テキスト エディターで*secrets.json*ファイルを開きます。 *secrets.json*の内容を、格納するキーと値のペアに置き換えます。 次に例を示します。

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 構造は、 または`dotnet user-secrets remove``dotnet user-secrets set`を使用して変更した後にフラット化されます。 たとえば、実行すると`dotnet user-secrets remove "Movies:ConnectionString"`オブジェクト リテラル`Movies`が折りたたまれます。 変更されたファイルは次のようになります。

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>複数のシークレットを設定する

シークレットのバッチは、`set`コマンドに JSON をパイプすることで設定できます。 次の例では *、input.json*ファイルの内容がコマンドに`set`パイプされます。

# <a name="windows"></a>[Windows](#tab/windows)

コマンド シェルを開き、次のコマンドを実行します。

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

コマンド シェルを開き、次のコマンドを実行します。

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>シークレットにアクセスする

[ASP.NETコア構成 API は](xref:fundamentals/configuration/index)シークレットマネージャーシークレットへのアクセスを提供します。

ASP.NET Core 2.0 以降では、プロジェクトがホストの新しいインスタンスを初期化するために事前設定された<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>既定値を使用して呼び出すと、ユーザー シークレット構成ソースが開発モードで自動的に追加されます。 `CreateDefaultBuilder`の<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>ときに呼び<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>出します。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

呼`CreateDefaultBuilder`び出されない場合は、コンストラクターで呼び出<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>して、ユーザー シークレット構成`Startup`ソースを明示的に追加します。 次`AddUserSecrets`の例に示すように、アプリケーションが開発環境で実行されている場合にのみ呼び出します。

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

ユーザーシークレットは`Configuration`API を使用して取得できます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>POCO にシークレットをマップする

オブジェクト リテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計できます。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

上記のシークレットを POCO にマップするには、API`Configuration`の[オブジェクト グラフ バインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。 次のコードは、カスタム`MovieSettings`POCO にバインドされ、`ServiceApiKey`プロパティ値にアクセスします。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`および`Movies:ServiceApiKey`シークレットは、 のそれぞれのプロパティにマップ`MovieSettings`されます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>文字列をシークレットに置き換える

パスワードをプレーンテキストで保存することは安全ではありません。 たとえば *、appsettings.json*に格納されているデータベース接続文字列には、指定したユーザーのパスワードが含まれる場合があります。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

より安全な方法は、パスワードを秘密として保存することです。 次に例を示します。

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json*の接続文字列からキーと値のペアを削除します。 次に例を示します。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

シークレットの値は、接続文字列を<xref:System.Data.SqlClient.SqlConnectionStringBuilder>完了するために、オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定できます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>秘密を一覧表示する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets list
```

次のような出力が表示されます。

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

前の例では、キー名のコロンは*secrets.json*内のオブジェクト階層を示します。

## <a name="remove-a-single-secret"></a>1 つのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

アプリの*secrets.json*ファイルが変更され、キーに関連付けられているキーと値`MoviesConnectionString`のペアが削除されました。

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`次のメッセージが表示されます。

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>すべてのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets clear
```

アプリのすべてのユーザー シークレットが*secrets.json*ファイルから削除されました。

```json
{}
```

[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>その他のリソース

* IIS からシークレット マネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[リック・アンダーソン](https://twitter.com/RickAndMSFT)、[ダニエル・ロス](https://github.com/danroth27)、[スコット・アディ](https://github.com/scottaddie)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このドキュメントでは、開発マシン上の ASP.NET Core アプリの開発中に機密データを格納および取得する方法について説明します。 パスワードやその他の機密データをソース コードに保存しないでください。 生産の秘密は、開発やテストに使用しないでください。 シークレットはアプリと共に展開しないでください。 代わりに、シークレットは、環境変数、Azure Key Vault などの制御された手段を使用して運用環境で使用できるようにする必要があります。Azure [Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを保存および保護できます。

## <a name="environment-variables"></a>環境変数

環境変数は、コードまたはローカル構成ファイルにアプリ シークレットが格納されないようにするために使用されます。 環境変数は、以前に指定したすべての構成ソースの構成値をオーバーライドします。

**個々のユーザー アカウント**のセキュリティが有効になっているASP.NETコア Web アプリを検討してください。 既定のデータベース接続文字列は、プロジェクトの*appsettings.json*ファイルにキー`DefaultConnection`を付けて含まれています。 既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB 用です。 アプリの展開中に`DefaultConnection`、キー値を環境変数の値でオーバーライドできます。 環境変数には、機密性の高い資格情報を含む完全な接続文字列が格納される場合があります。

> [!WARNING]
> 環境変数は、通常、暗号化されていないプレーン テキストに格納されます。 マシンまたはプロセスが侵害された場合、環境変数は信頼されていないパーティからアクセスできます。 ユーザーの秘密の漏えいを防ぐための追加措置が必要な場合があります。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>シークレットマネージャー

シークレット マネージャー ツールは、ASP.NET コア プロジェクトの開発中に機密データを格納します。 このコンテキストでは、機密データの一部はアプリの秘密です。 アプリ シークレットは、プロジェクト ツリーとは別の場所に格納されます。 アプリ シークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクトで共有されます。 アプリシークレットはソース管理にチェックインされません。

> [!WARNING]
> シークレット マネージャー ツールは、保存されているシークレットを暗号化しないので、信頼できるストアとして扱う必要はありません。 開発目的のみのためです。 キーと値は、ユーザー プロファイル ディレクトリの JSON 設定ファイルに格納されます。

## <a name="how-the-secret-manager-tool-works"></a>シークレットマネージャーツールの仕組み

シークレット マネージャー ツールは、値が格納される場所や方法など、実装の詳細を抽象化します。 これらの実装の詳細を知らなくても、このツールを使用できます。 値は、ローカル コンピューター上のシステムで保護されたユーザー プロファイル フォルダーに JSON 構成ファイルに格納されます。

# <a name="windows"></a>[Windows](#tab/windows)

ファイル システム パス:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

ファイル システム パス:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

上記のファイル パスで`<user_secrets_id>`*、.csproj*ファイルで指定された`UserSecretsId`値に置き換えます。

シークレット マネージャー ツールで保存されたデータの場所や形式に依存するコードを記述しないでください。 これらの実装の詳細は変更される場合があります。 たとえば、シークレット値は暗号化されませんが、将来の値になる可能性があります。

## <a name="enable-secret-storage"></a>シークレット ストレージを有効にする

シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクト固有の構成設定に対して動作します。

ユーザー シークレットを使用するには`UserSecretsId`*、.csproj*ファイル内の`PropertyGroup`要素を定義します。 の内部テキスト`UserSecretsId`は任意ですが、プロジェクトに固有です。 開発者は通常、 の`UserSecretsId`GUID を生成します。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio で、ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューから **[ユーザー シークレットの管理**] を選択します。 このジェスチャは`UserSecretsId`*、.csproj*ファイルに GUID を設定した要素を追加します。

## <a name="set-a-secret"></a>秘密を設定する

キーとその値で構成されるアプリ シークレットを定義します。 シークレットはプロジェクトの`UserSecretsId`値に関連付けられます。 たとえば *、.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

上の例では、コロンはプロパティを`Movies`持つオブジェクト リテラルを`ServiceApiKey`示しています。

シークレットマネージャツールは、他のディレクトリからも使用できます。 `--project` *.csproj*ファイルが存在するファイル システム パスを指定するには、このオプションを使用します。 次に例を示します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>ビジュアル スタジオでの JSON 構造のフラット化

Visual Studio の**ユーザー シークレットの管理**ジェスチャは、テキスト エディターで*secrets.json*ファイルを開きます。 *secrets.json*の内容を、格納するキーと値のペアに置き換えます。 次に例を示します。

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 構造は、 または`dotnet user-secrets remove``dotnet user-secrets set`を使用して変更した後にフラット化されます。 たとえば、実行すると`dotnet user-secrets remove "Movies:ConnectionString"`オブジェクト リテラル`Movies`が折りたたまれます。 変更されたファイルは次のようになります。

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>複数のシークレットを設定する

シークレットのバッチは、`set`コマンドに JSON をパイプすることで設定できます。 次の例では *、input.json*ファイルの内容がコマンドに`set`パイプされます。

# <a name="windows"></a>[Windows](#tab/windows)

コマンド シェルを開き、次のコマンドを実行します。

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

コマンド シェルを開き、次のコマンドを実行します。

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>シークレットにアクセスする

[ASP.NETコア構成 API は](xref:fundamentals/configuration/index)シークレットマネージャーシークレットへのアクセスを提供します。

プロジェクトが .NET Framework を対象とする場合は、[パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)をインストールします。


ASP.NET Core 2.0 以降では、プロジェクトがホストの新しいインスタンスを初期化するために事前設定された<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>既定値を使用して呼び出すと、ユーザー シークレット構成ソースが開発モードで自動的に追加されます。 `CreateDefaultBuilder`の<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>ときに呼び<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>出します。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


呼`CreateDefaultBuilder`び出されない場合は、コンストラクターで呼び出<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>して、ユーザー シークレット構成`Startup`ソースを明示的に追加します。 次`AddUserSecrets`の例に示すように、アプリケーションが開発環境で実行されている場合にのみ呼び出します。

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

ユーザーシークレットは`Configuration`API を使用して取得できます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>POCO にシークレットをマップする

オブジェクト リテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計できます。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

上記のシークレットを POCO にマップするには、API`Configuration`の[オブジェクト グラフ バインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。 次のコードは、カスタム`MovieSettings`POCO にバインドされ、`ServiceApiKey`プロパティ値にアクセスします。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`および`Movies:ServiceApiKey`シークレットは、 のそれぞれのプロパティにマップ`MovieSettings`されます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>文字列をシークレットに置き換える

パスワードをプレーンテキストで保存することは安全ではありません。 たとえば *、appsettings.json*に格納されているデータベース接続文字列には、指定したユーザーのパスワードが含まれる場合があります。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

より安全な方法は、パスワードを秘密として保存することです。 次に例を示します。

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json*の接続文字列からキーと値のペアを削除します。 次に例を示します。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

シークレットの値は、接続文字列を<xref:System.Data.SqlClient.SqlConnectionStringBuilder>完了するために、オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>プロパティに設定できます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>秘密を一覧表示する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets list
```

次のような出力が表示されます。

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

前の例では、キー名のコロンは*secrets.json*内のオブジェクト階層を示します。

## <a name="remove-a-single-secret"></a>1 つのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

アプリの*secrets.json*ファイルが変更され、キーに関連付けられているキーと値`MoviesConnectionString`のペアが削除されました。

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>すべてのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets clear
```

アプリのすべてのユーザー シークレットが*secrets.json*ファイルから削除されました。

```json
{}
```

[`dotnet user-secrets list`実行中] を選択すると、次のメッセージが表示されます。

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>その他のリソース

* IIS からシークレット マネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end