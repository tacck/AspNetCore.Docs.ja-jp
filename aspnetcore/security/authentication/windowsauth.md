---
title: ASP.NET Core で Windows 認証を構成する
author: scottaddie
description: IIS および HTTP.sys の ASP.NET Core で Windows 認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330686"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core で Windows 認証を構成する

作成者: [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) は、 [IIS](xref:host-and-deploy/iis/index)、 [kestrel](xref:fundamentals/servers/kestrel)、 [HTTP.sys](xref:fundamentals/servers/httpsys)でホストされている ASP.NET Core アプリに対して構成できます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) は、 [IIS](xref:host-and-deploy/iis/index)または[HTTP.sys](xref:fundamentals/servers/httpsys)でホストされる ASP.NET Core アプリ用に構成できます。

::: moniker-end

Windows 認証では、オペレーティングシステムに依存して ASP.NET Core アプリのユーザーを認証します。 サーバーが企業ネットワーク上で Active Directory ドメイン id または Windows アカウントを使用してユーザーを識別する場合は、Windows 認証を使用できます。 Windows 認証は、ユーザー、クライアントアプリ、および web サーバーが同じ Windows ドメインに属しているイントラネット環境に最適です。

> [!NOTE]
> Windows 認証は、HTTP/2 ではサポートされていません。 認証チャレンジは HTTP/2 応答で送信できますが、認証する前にクライアントを HTTP/1.1 にダウングレードする必要があります。

## <a name="proxy-and-load-balancer-scenarios"></a>プロキシとロードバランサーのシナリオ

Windows 認証は、主にイントラネットで使用されるステートフルなシナリオであり、プロキシまたはロードバランサーは通常、クライアントとサーバー間のトラフィックを処理しません。 プロキシまたはロードバランサーが使用されている場合、Windows 認証はプロキシまたはロードバランサーの場合にのみ機能します。

* 認証を処理します。
* 認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。

プロキシとロードバランサーを使用する環境での Windows 認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。

## <a name="iisiis-express"></a>IIS/IIS Express

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>で (名前空間) を呼び出して認証サービスを追加し <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> `Startup.ConfigureServices` ます。

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>起動設定 (デバッガー)

起動設定の構成は、IIS Express のファイルの*プロパティ/launchSettings.jsに*のみ影響し、Windows 認証用に IIS を構成することはありません。 サーバーの構成については、「 [IIS](#iis) 」セクションを参照してください。

Visual Studio または .NET Core CLI で使用できる**Web アプリケーション**テンプレートは、Windows 認証をサポートするように構成できます。これにより、ファイルの*プロパティや launchSettings.js*が自動的に更新されます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**新しいプロジェクト**

1. 新しいプロジェクトを作成します。
1. **[ASP.NET Core Web アプリケーション]** を選択します。 **[次へ]** を選択します。
1. [**プロジェクト名**] フィールドに名前を入力します。 **[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。 **[作成]** を選択します。
1. [**認証**] で [**変更**] を選択します。
1. [**認証の変更**] ウィンドウで、[ **Windows 認証**] を選択します。 **[OK]** を選択します。
1. **[Web アプリケーション]** を選択します。
1. **［作成］** を選択します

アプリケーションを実行します。 ユーザー名は、表示されるアプリのユーザーインターフェイスに表示されます。

**既存のプロジェクト**

プロジェクトのプロパティは、Windows 認証を有効にし、匿名認証を無効にします。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[プロパティ]** を選択します。
1. **[デバッグ]** タブを選択します。
1. [**匿名認証を有効にする**] のチェックボックスをオフにします。
1. [ **Windows 認証を有効にする**] チェックボックスをオンにします。
1. プロパティページを保存して閉じます。

または、ファイルのlaunchSettings.jsのノードでプロパティを構成することもでき `iisSettings` ます。 *launchSettings.json*

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**新しいプロジェクト**

引数 (ASP.NET Core Web App) を指定して[dotnet new](/dotnet/core/tools/dotnet-new)コマンドを実行 `webapp` し、スイッチを指定し `--auth Windows` ます。

```dotnetcli
dotnet new webapp --auth Windows
```

**既存のプロジェクト**

ファイルの `iisSettings` *launchSettings.js*のノードを更新します。

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

既存のプロジェクトを変更する場合は、プロジェクトファイルに[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)**また**は[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet パッケージのパッケージ参照が含まれていることを確認します。

### <a name="iis"></a>IIS

IIS では、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して ASP.NET Core アプリをホストします。 Windows 認証は、 *web.config*ファイルを介して IIS 用に構成されます。 以下のセクションでは、その方法について説明します。

* アプリの展開時にサーバーで Windows 認証をアクティブ化するローカル*web.config*ファイルを指定します。
* サーバーに既に配置されている ASP.NET Core アプリの*web.config*ファイルを構成するには、IIS マネージャーを使用します。

まだインストールしていない場合は、IIS で ASP.NET Core アプリをホストできるようにします。 詳細については、「<xref:host-and-deploy/iis/index>」を参照してください。

Windows 認証で IIS 役割サービスを有効にします。 詳細については、「 [IIS 役割サービスで Windows 認証を有効にする (手順2参照)](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。

[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)は、既定で自動的に要求を認証するように構成されています。 詳細については、「 [iis を使用した Windows でのホスト ASP.NET Core: iis オプション (自動認証)](xref:host-and-deploy/iis/index#iis-options)」を参照してください。

ASP.NET Core モジュールは、既定で Windows 認証トークンをアプリに転送するように構成されています。 詳細については、「 [ASP.NET Core モジュール構成リファレンス: aspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。

次の**いずれか**の方法を使用します。

* **プロジェクトを発行して配置する前に、** 次の*web.config*ファイルをプロジェクトのルートに追加します。

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  プロジェクトが .NET Core SDK によって発行された場合 (プロジェクトファイルのプロパティがに設定されていない場合 `<IsTransformWebConfigDisabled>` `true` )、発行された*web.config*ファイルにはセクションが含まれ `<location><system.webServer><security><authentication>` ます。 プロパティの詳細につい `<IsTransformWebConfigDisabled>` ては、「」を参照してください <xref:host-and-deploy/iis/index#webconfig-file> 。

* **プロジェクトを発行および配置した後、** IIS マネージャーを使用してサーバー側の構成を実行します。

  1. IIS マネージャーで、[**接続**] サイドバーの [**サイト**] ノードの下の [iis] サイトを選択します。
  1. **IIS**領域で [**認証**] をダブルクリックします。
  1. [**匿名認証**] を選択します。 [**アクション**] サイドバーで [**無効**] を選択します。
  1. **[Windows 認証]** をクリックします。 [**アクション**] サイドバーで [**有効化**] を選択します。

  これらの操作を実行すると、IIS マネージャーによって、アプリの*web.config*ファイルが変更されます。 `<system.webServer><security><authentication>`ノードは、およびの更新された設定を使用して追加され `anonymousAuthentication` `windowsAuthentication` ます。

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`IIS マネージャーによって*web.config*ファイルに追加されたセクションは、 `<location>` アプリの発行時に .NET Core SDK によって追加されたアプリのセクションの外部にあります。 セクションはノードの外側に追加されるため `<location>` 、設定は[サブアプリ](xref:host-and-deploy/iis/index#sub-applications)によって現在のアプリに継承されます。 継承を防止するには、 `<security>` .NET Core SDK 提供されたセクション内に追加されたセクションを移動し `<location><system.webServer>` ます。

  Iis マネージャーを使用して IIS 構成を追加すると、サーバー上のアプリの*web.config*ファイルにのみ影響します。 サーバーの*web.config*のコピーがプロジェクトの*web.config*ファイルで置き換えられた場合、その後のアプリの展開では、サーバーの設定が上書きされる可能性があります。 設定を管理するには、次の**いずれか**の方法を使用します。

  * 展開時にファイルが上書きされた後、IIS マネージャーを使用して、 *web.config*ファイルの設定をリセットします。
  * 設定を使用して、アプリケーションに*web.config ファイル*をローカルに追加します。

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Windows、Linux、macOS で Negotiate と Kerberos を使用した Windows 認証をサポートするために、 [Kestrel](xref:fundamentals/servers/kestrel)と共に[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet パッケージを使用できます。

> [!WARNING]
> 資格情報は、接続時に要求間で永続化できます。 *プロキシが Kestrel と1:1 の接続関係 (永続的な接続) を維持していない限り、プロキシでネゴシエート認証を使用することはできません。*

> [!NOTE]
> Negotiate ハンドラーは、基になるサーバーが Windows 認証をネイティブでサポートしているかどうかを検出します (有効になっている場合)。 サーバーが Windows 認証をサポートしていても無効になっている場合は、サーバーの実装を有効にするように求めるエラーがスローされます。 サーバーで Windows 認証が有効になっている場合は、ネゴシエートハンドラーによって透過的に転送されます。

でおよびを呼び出して、認証サービスを追加 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> し <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` ます。

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

でを呼び出して認証ミドルウェアを追加し <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` ます。

 ```csharp
app.UseAuthentication();
```

ミドルウェアの詳細については、「」を参照してください <xref:fundamentals/middleware/index> 。

匿名要求が許可されます。 [ASP.NET Core 承認](xref:security/authorization/introduction)を使用して、認証のための匿名要求をチャレンジします。

### <a name="windows-environment-configuration"></a>Windows 環境構成

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)コンポーネントは、ユーザーモード認証を実行します。 サービスプリンシパル名 (Spn) は、コンピューターアカウントではなく、サービスを実行しているユーザーアカウントに追加する必要があります。 `setspn -S HTTP/myservername.mydomain.com myuser`管理コマンドシェルでを実行します。

### <a name="linux-and-macos-environment-configuration"></a>Linux および macOS 環境構成

Linux または macOS コンピューターを Windows ドメインに参加させる手順については、「 [windows 認証を使用した SQL Server への Azure Data Studio の接続](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)」を参照してください。 この手順では、ドメイン上の Linux マシン用のコンピューターアカウントを作成します。 Spn をそのコンピューターアカウントに追加する必要があります。

> [!NOTE]
> 「 [Windows 認証を使用して SQL Server に Azure Data Studio 接続する-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 」のガイダンスに従っている場合は、必要に応じ `python-software-properties` てをに置き換え `python3-software-properties` ます。

Linux または macOS コンピューターがドメインに参加したら、次の手順に従って、[キータブファイル](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)に spn を指定する必要があります。

* ドメインコントローラーで、新しい web サービス Spn をコンピューターアカウントに追加します。
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* [Ktpass](/windows-server/administration/windows-commands/ktpass)を使用して、キータブファイルを生成します。
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * 一部のフィールドは、示されているとおりに大文字で指定する必要があります。
* キータブファイルを Linux または macOS マシンにコピーします。
* 環境変数を使用して、キーボックスファイルを選択します。`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* `klist`を呼び出して、現在使用可能な spn を表示します。

> [!NOTE]
> キータブファイルにはドメインアクセス資格情報が含まれており、それに従って保護する必要があります。

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys)は、ネゴシエート、NTLM、または基本認証を使用したカーネルモードの Windows 認証をサポートしています。

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>で (名前空間) を呼び出して認証サービスを追加し <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> `Startup.ConfigureServices` ます。

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Windows 認証 (*Program.cs*) で HTTP.sys を使用するようにアプリの web ホストを構成します。 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>は <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 名前空間にあります。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。 Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。 Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。 アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。

> [!NOTE]
> HTTP.sys は、Nano Server バージョン1709以降ではサポートされていません。 Nano Server で Windows 認証と HTTP.sys を使用するには、 [Server Core (microsoft/windowsservercore) コンテナー](https://hub.docker.com/r/microsoft/windowsservercore/)を使用します。 Server Core の詳細については、「 [Windows server の Server core インストールオプション](/windows-server/administration/server-core/what-is-server-core)について」を参照してください。

## <a name="authorize-users"></a>ユーザーの承認

匿名アクセスの構成の状態によって、 `[Authorize]` アプリで属性と属性がどのように使用されるかが決まり `[AllowAnonymous]` ます。 次の2つのセクションでは、匿名アクセスの許可されていない構成と許可される構成の状態の処理方法について説明します。

### <a name="disallow-anonymous-access"></a>匿名アクセスを許可しない

Windows 認証が有効になっていて、匿名アクセスが無効になっている場合、 `[Authorize]` `[AllowAnonymous]` 属性と属性は影響を与えません。 IIS サイトが匿名アクセスを許可しないように構成されている場合、要求はアプリに到達しません。 このため、属性は `[AllowAnonymous]` 適用されません。

### <a name="allow-anonymous-access"></a>匿名アクセスを許可する

Windows 認証と匿名アクセスの両方が有効になっている場合は、 `[Authorize]` 属性と属性を使用し `[AllowAnonymous]` ます。 `[Authorize]`属性を使用すると、認証を必要とするアプリのエンドポイントをセキュリティで保護することができます。 属性は、 `[AllowAnonymous]` `[Authorize]` 匿名アクセスを許可するアプリの属性よりも優先されます。 属性の使用の詳細については、「」を参照してください <xref:security/authorization/simple> 。

> [!NOTE]
> 既定では、ページにアクセスする権限を持たないユーザーには、空の HTTP 403 応答が表示されます。 [Statuscodepages ページミドルウェア](xref:fundamentals/error-handling#usestatuscodepages)は、ユーザーが "アクセス拒否" のエクスペリエンスを向上させるように構成できます。

## <a name="impersonation"></a>偽装

ASP.NET Core が偽装を実装していません。 アプリは、アプリプールまたはプロセス id を使用して、すべての要求に対してアプリの id で実行されます。 アプリでユーザーに代わってアクションを実行する必要がある場合は、の[ターミナルインラインミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)で[WindowsIdentity](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)を使用します `Startup.Configure` 。 このコンテキストで1つのアクションを実行し、コンテキストを閉じます。

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`は非同期操作をサポートしていないため、複雑なシナリオでは使用できません。 たとえば、要求またはミドルウェアチェーン全体のラップはサポートされていないか、推奨されません。

::: moniker range=">= aspnetcore-3.0"

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)パッケージは Windows、Linux、および macOS での認証を有効にしますが、偽装は windows でのみサポートされています。

::: moniker-end

## <a name="claims-transformations"></a>要求の変換

::: moniker range=">= aspnetcore-3.0"

IIS でホストする場合、は、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> ユーザーを初期化するために内部では呼び出されません。 そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。 クレーム変換をアクティブにするコード例については、「」を参照してください <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

IIS インプロセスモードでホストする場合、は、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> ユーザーを初期化するために内部的に呼び出されません。 そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。 インプロセスをホストするときに要求変換をアクティブにするコード例と詳細については、「」を参照してください <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
