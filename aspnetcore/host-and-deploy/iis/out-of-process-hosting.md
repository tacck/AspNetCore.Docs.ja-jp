---
title: IIS と ASP.NET Core を使用したアウトプロセス ホスティング
author: rick-anderson
description: IIS と ASP.NET Core モジュールを使用したアウトプロセス ホスティングについて説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060418"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>IIS と ASP.NET Core を使用したアウトプロセス ホスティング 

ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。 モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。 この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。

次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

1. 要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。
1. ドライバーは、Web サイトの構成ポートで IIS への要求をルーティングします。 構成されるポートは、通常 80 (HTTP) または 443 (HTTPS) です。
1. モジュールでは、アプリのランダムなポートで Kestrel に要求を転送します。 ランダムなポートは 80 または 443 ではありません。

<!-- make this a bullet list -->
ASP.NET Core モジュールは、起動時に環境変数によってポートを指定します。 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 拡張機能は、`http://localhost:{PORT}` でリッスンするようにサーバーを構成します。 追加のチェックが実行され、モジュールから発生していない要求は拒否されます。 モジュールは HTTPS 転送をサポートしていません。 要求は HTTPS を介して IIS によって受信された場合でも HTTP を介して転送されます。

Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインに転送されます。 ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。 IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。 アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。

ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。

ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。

## <a name="application-configuration"></a>アプリケーション構成

### <a name="enable-the-iisintegration-components"></a>IISIntegration コンポーネントを有効にする

`CreateHostBuilder` (`Program.cs`) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> を呼び出して IIS 統合を有効にします。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。


**アウトプロセス ホスティング モデル**

IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。 次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| オプション                         | Default | 設定 |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。 `false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。 `AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。 詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。 |
| `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。 |
| `ForwardClientCertificate`     | `true`  | `true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。 |


### <a name="proxy-server-and-load-balancer-scenarios"></a>プロキシ サーバーとロード バランサーのシナリオ

[IIS 統合ミドルウェア](#enable-the-iisintegration-components)と ASP.NET Core モジュールは、次を転送するように構成されています。

* スキーム (HTTP/HTTPS)。
* 要求元のリモート IP アドレス。

[IIS 統合ミドルウェア](#enable-the-iisintegration-components)は、Forwarded Headers Middleware を構成します。

追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。


### <a name="out-of-process-hosting-model"></a>アウト プロセス ホスティング モデル

アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( `.csproj`) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

インプロセス ホスティングは `InProcess` で設定され、これが既定値です。

`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。

IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。

アウトプロセスの場合は、[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> が呼び出され、次のことが行われます。

* ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。
* スタートアップ エラーをキャプチャするホストを構成します。

### <a name="process-name"></a>プロセス名

`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。

Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。 ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。

ASP.NET Core モジュールでは次のことも行えます。

* ワーカー プロセスの環境変数を設定する
* 起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する
* Windows 認証トークンを転送する
