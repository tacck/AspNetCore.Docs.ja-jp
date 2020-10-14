---
title: web.config ファイル
author: rick-anderson
description: web.config ファイルの内容と、さまざまな ASP.NET Core モジュール オプションの構成方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755193"
---
# <a name="webconfig-file"></a>`web.config` ファイル

`web.config` は IIS によって、および IIS でホストされるアプリを構成するための [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)によって読み取られるファイルです。

## <a name="webconfig-file-location"></a>`web.config` ファイルの場所

[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、`web.config` ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。 これは、IIS に提供される web サイトの物理パスと同じ場所です。 `web.config` ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。

アプリの物理パスには、`{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml` (XML ドキュメントのコメント)、`{ASSEMBLY}.deps.json` (プレースホルダー `{ASSEMBLY}` はアセンブリ名) などの機密性の高いファイルが存在します。 `web.config` ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。 `web.config`ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。

**`web.config`ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。`web.config` ファイルを運用環境の展開から削除しないでください。**

プロジェクト内に `web.config` ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な `processPath` と `arguments` を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。

プロジェクトに `web.config` ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい `processPath` と `arguments` を使用して作成され、発行された出力に移行されます。 変換によりファイル内の IIS 構成の設定が変わることはありません。

`web.config` ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。 ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。

`web.config` ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。 このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。 SDK は、プロジェクト ファイルの先頭で設定されています。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Web SDK によって `web.config` ファイルが変換されないようにするため、`<IsTransformWebConfigDisabled>` プロパティをプロジェクト ファイルで使用します。

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK ファイルの変換を無効にすると、 `processPath`と`arguments`開発者によって手動で設定する必要があります。 詳細については、<xref:host-and-deploy/aspnet-core-module> を参照してください。

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>`web.config` を使用した ASP.NET Core モジュールの構成

ASP.NET Core モジュールは、サイトの `web.config` ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。

次に示す `web.config` ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

次の `web.config` は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> プロパティは、`false` に設定されます。これは、[`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。

アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。 パスは stdout ログを `LogFiles` フォルダーに保存します。これは、サービスによって自動的に作成される場所です。

IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。

### <a name="attributes-of-the-aspnetcore-element"></a>`aspNetCore` 要素の属性

| 属性 | 説明 | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>省略可能な文字列属性。</p><p>`processPath` において指定されている実行可能ファイルへの引数です。</p> | |
| `disableStartUpErrorPage` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**502.5 - 処理エラー** ページは抑制され、`web.config` で構成されている 502 状態コード ページが優先されます。</p> | `false` |
| `forwardWindowsAuthToken` | <p>省略可能な Boolean 属性です。</p><p>true の場合、トークンは、`%ASPNETCORE_PORT%` 上でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。 要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</p> | `true` |
| `hostingModel` | <p>省略可能な文字列属性。</p><p>ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>省略可能な整数属性</p><p>アプリごとにスピンアップすることができる `processPath` 設定内で指定したプロセスのインスタンス数が指定されます。</p><p>&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</p><p>設定 `processesPerApplication` は推奨されません。 この属性は将来のリリースで削除されます。</p> | 既定値: `1`<br>最小値: `1`<br>最大値: `100`&dagger; |
| `processPath` | <p>必須の文字列属性です。</p><p>HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。 相対パスがサポートされています。 パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</p> | |
| `rapidFailsPerMinute` | <p>省略可能な整数属性</p><p>`processPath` で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。 この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</p><p>インプロセス ホスティングではサポートされていません。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `100` |
| `requestTimeout` | <p>省略可能な期間属性。</p><p>%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</p><p>ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</p><p>インプロセス ホスティングには適用されません。 インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</p><p>0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。 分または秒の値に `60` を入れると *500 - Internal Server Error* が出ます。</p> | 既定値: `00:02:00`<br>最小値: `00:00:00`<br>最大値: `360:00:00` |
| `shutdownTimeLimit` | <p>省略可能な整数属性</p><p>`app_offline.htm` ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `600` |
| `startupTimeLimit` | <p>省略可能な整数属性</p><p>ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。 この制限時間を超えた場合、モジュールはプロセスを強制終了します。</p><p>"*インプロセス*" でホスティングしている場合: プロセスは再起動されて**おらず**、`rapidFailsPerMinute` 設定が使用されて**いません**。</p><p>"*アウト プロセス*" でホスティングしている場合: 最後のローリング分においてアプリが `rapidFailsPerMinute` 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</p><p>値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</p> | 既定値: `120`<br>最小値: `0`<br>最大値: `3600` |
| `stdoutLogEnabled` | <p>省略可能な Boolean 属性です。</p><p>true の場合、`processPath` で指定されているプロセスの `stdout` と `stderr` は、`stdoutLogFile` で指定されているファイルにリダイレクトされます。</p> | `false` |
| `stdoutLogFile` | <p>省略可能な文字列属性。</p><p>`processPath` で指定されているプロセスからの `stdout` と `stderr` がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。 相対パスの基準はサイトのルートです。 `.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。 パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。 アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) が、`stdoutLogFile` パスの最後のセグメントに追加されます。 たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは `logs` フォルダーに `stdout_20180205194132_1934.log` として保存されます。</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>環境変数を設定する

`processPath` 属性で、プロセスに対する環境変数を指定できます。 `<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。 このセクションで設定された環境変数は、システム環境変数より優先されます。

次の例では、`web.config` 内に 2 つの環境変数が設定されています。 `ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。 開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、`web.config` ファイルでこの値を一時的に設定できます。 `CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> `web.config` 内で環境を直接設定する代わりに、[発行プロファイル (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。 この方法では、プロジェクトが発行されるときに `web.config` に環境が設定されます。
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。

## <a name="configuration-of-iis-with-webconfig"></a>`web.config` を使用した IIS の構成

IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの `web.config` の `<system.webServer>` セクションによる影響を受けます。 たとえば、IIS の構成は動的な圧縮で機能します。 IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの `web.config` ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。

詳細については、次のトピックを参照してください。

* [`<system.webServer>` の構成リファレンス](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、「[環境変数`<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)」のトピックにある " *`AppCmd.exe` コマンド*" のセクションを参照してください。

## <a name="configuration-sections-of-webconfig"></a>`web.config` の構成セクション

`web.config` の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。 詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。

## <a name="transform-webconfig"></a>web.config を変換する

発行時に "`web.config`" を変換する場合は、「<xref:host-and-deploy/iis/transform-webconfig>」を参照してください。 発行時に "`web.config`" を変換して、構成、プロファイル、環境に基づいた環境変数を設定しなければならない場合があります。

## <a name="additional-resources"></a>その他の資料

* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
