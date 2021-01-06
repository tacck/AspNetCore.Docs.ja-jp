---
title: IIS と ASP.NET Core を使用したインプロセス ホスティング
author: rick-anderson
description: IIS と ASP.NET Core モジュールを使用したインプロセス ホスティングについて説明します。
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058481"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>IIS と ASP.NET Core を使用したインプロセス ホスティング 

インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。 インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。

次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>インプロセス ホスティングを有効にする

ASP.NET Core 3.0 以降、IIS に展開されるすべてのアプリに対してインプロセス ホスティングが既定では有効になっています。

インプロセス ホスティング用にアプリを明示的に構成するには、プロジェクト ファイル (`.csproj`) で、`<AspNetCoreHostingModel>` プロパティの値を `InProcess` に設定します。

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>全般的なアーキテクチャ

要求の一般的なフローは次のとおりです。

1. Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。
1. このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。
1. ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。 IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。

IIS HTTP サーバーによって要求が処理された後は、次のようになります。

1. 要求は ASP.NET Core ミドルウェア パイプラインに送信されます。
1. ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。
1. アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。
1. IIS は、要求を開始したクライアントに応答を送信します。

`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (`w3wp.exe` または `iisexpress.exe`) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。 パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) へのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。

単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。

## <a name="application-configuration"></a>アプリケーションの構成

IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。 次の例では、AutomaticAuthentication が無効になります。

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| オプション | Default | 設定 |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | `true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。 `false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。 `AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。 詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。 |
| `AuthenticationDisplayName` | `null` | ログイン ページでユーザーに表示名が表示されるように設定します。 |
| `AllowSynchronousIO` | `false` | `HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるか。 |
| `MaxRequestBodySize` | `30000000` | `HttpRequest` の最大要求本文サイズを取得または設定します。 IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。 `MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。 `maxAllowedContentLength`を引き上げるには、`web.config` 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。 詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。 |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>インプロセスおよびアウトプロセス ホスティングの相違点

インプロセスでホストする場合は、次の特性が適用されます。

* [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。 インプロセスの場合は、[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> が呼び出され、次のことが行われます。

  * `IISHttpServer` を登録します。
  * ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。
  * スタートアップ エラーをキャプチャするホストを構成します。

* [`requestTimeout` 属性](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。

* アプリ プールをアプリ間で共有することはサポートされていません。 アプリごとに 1 つのアプリ プールを使用します。

* アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。 たとえば、32 ビット (x86) 用に公開されたアプリの場合、IIS アプリケーション プールで 32 ビットが有効になっている必要があります。 詳細については、「[IIS サイトを作成する](xref:tutorials/publish-to-iis#create-the-iis-site)」セクションを参照してください。

* クライアントの切断が検出されます。 クライアントが切断されると、[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) キャンセル トークンが取り消されます。

* インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> が呼び出されることはありません。 そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> を呼び出し、認証サービスを追加します。

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。
