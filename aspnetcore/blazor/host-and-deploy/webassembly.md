---
title: ASP.NET Core Blazor WebAssembly のホストと展開
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: dadf6076e7f07c07381856aa225667a6eb38046a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080317"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly のホストと展開

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)

[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用する場合は以下のようになります。

* Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。
* アプリがブラウザー UI スレッド上で直接実行されます。

次の展開戦略がサポートされています。

* Blazor アプリは、ASP.NET Core アプリによって提供されます。 この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。
* Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。 この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。

## <a name="compression"></a>[圧縮]

Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。 次の圧縮アルゴリズムが使用されます。

* [Brotli](https://tools.ietf.org/html/rfc7932) (最高レベル)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor は、適切な圧縮ファイルにサービスを提供するため、ホストに依存します。 ASP.NET Core でホストするプロジェクトを使用するとき、ホスト プロジェクトでは、コンテント ネゴシエーションを実行したり、静的に圧縮されたファイルにサービスを提供したりできます。 Blazor WebAssembly スタンドアロン アプリをホストするとき、静的に圧縮されたファイルにサービスが提供されるよう、追加の作業が必要になることがあります。

* IIS の `web.config` の圧縮構成については、[IIS の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。 
* GitHub ページなど、静的に圧縮されたファイル コンテント ネゴシエーションをサポートしない静的ホスティング ソリューションでホストするとき、Brotli 圧縮ファイルをフェッチし、デコードするようにアプリを構成することを検討してください。

  * [google/brotli GitHub リポジトリ](https://github.com/google/brotli)から、JavaScript Brotli デコーダーを入手します。 2020 年 7 月の時点で、デコーダー ファイルは `decode.min.js` という名前で、リポジトリの [`js` フォルダー](https://github.com/google/brotli/tree/master/js)にあります。
  * そのデコーダーを使用するようにアプリを更新します。 `wwwroot/index.html` の終了タグ `<body>` に含まれるマークアップを次のように変更します。
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
圧縮を無効にするには、アプリケーションのプロジェクト ファイルに `BlazorEnableCompression` MSBuild プロパティを追加し、値を `false` に設定します。

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

`BlazorEnableCompression` プロパティは、コマンド シェルで次の構文を使用して [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドに渡すことができます。

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>正しいルーティングのために URL を書き換える

Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor Server (ホストされているアプリ) でのルーティング要求のように単純なものではありません。 次の 2 つのコンポーネントがある Blazor WebAssembly アプリについて考えてみます。

* `Main.razor`:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。
* `About.razor`: `About` コンポーネント。

アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:

1. ブラウザーにより要求が送信されます。
1. 既定のページ (通常は `index.html`) が返されます。
1. `index.html` によりアプリがブートストラップされます。
1. Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。

Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。 " *Blazor WebAssembly アプリ内にある*" 内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。 要求は、ルーターによって内部的に処理されます。

ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。 アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。

ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する `index.html` ページへのすべての要求を、書き換える必要があります。 `index.html` が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。

IIS サーバーに展開する場合は、アプリで発行される `web.config` ファイルで URL Rewrite Module を使用できます。 詳細については、「[IIS](#iis)」セクションを参照してください。

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core でのホストされた展開

"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。

クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。 2 つのアプリが一緒に展開されます。 ASP.NET Core アプリをホストできる Web サーバーが必要です。 ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **`Hosted`** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。

ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。

Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>複数の Blazor WebAssembly アプリによるホストされた展開

### <a name="app-configuration"></a>アプリの構成

複数の Blazor WebAssembly アプリを提供するようにホストされた Blazor ソリューションを構成するには:

* 既存のホストされた Blazor ソリューションを使用するか、Blazor のホストされたプロジェクト テンプレートから新しいソリューションを作成します。

* クライアント アプリのプロジェクト ファイルで、値が `FirstApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加して、プロジェクトの静的アセットの基本パスを設定します。

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* ソリューションに 2 つ目のクライアント アプリを追加します。

  * `SecondClient` という名前のフォルダーをソリューションのフォルダーに追加します。
  * Blazor WebAssembly プロジェクト テンプレートから `SecondClient` フォルダーに `SecondBlazorApp.Client` という名前の Blazor WebAssembly アプリを作成します。
  * アプリのプロジェクト ファイル内で:

    * 値が `SecondApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加します。

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * `Shared` プロジェクトにプロジェクト参照を追加します。

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      プレースホルダー `{SOLUTION NAME}` は、ソリューションの名前です。

* サーバー アプリのプロジェクト ファイルで、追加したクライアン トアプリに対するプロジェクト参照を作成します。

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* サーバー アプリの `Properties/launchSettings.json` ファイルで、ポート 5001 と 5002 でクライアント アプリにアクセスするように、Kestrel プロファイル (`{SOLUTION NAME}.Server`) の `applicationUrl` を構成します。

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* サーバー アプリの `Startup.Configure` メソッド (`Startup.cs`) で、<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> の呼び出しの後にある次の行を削除します。

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  要求をクライアント アプリにマップするミドルウェアを追加します。 次の例では、ミドルウェアが以下のときに実行されるように構成します。

  * 要求のポートが、元のクライアント アプリの場合は 5001、追加されたクライアント アプリの場合は 5002 である。
  * 要求のホストが、元のクライアント アプリの場合は `firstapp.com`、追加されたクライアント アプリの場合は `secondapp.com` である。

    > [!NOTE]
    > このセクションで示されている例では、以下に対する追加構成が必要です。
    >
    > * 例のホスト ドメイン `firstapp.com` および `secondapp.com` でのアプリへのアクセス。
    > * クライアント アプリで TLS セキュリティ (HTTPS) を有効にするための証明書。
    >
    > 必要な構成はこの記事の範囲を超えており、ソリューションのホスト方法によって異なります。 詳細については、[ホストと展開に関する記事](xref:host-and-deploy/index)を参照してください。

  前に行を削除した場所に、次のコードを配置します。

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* サーバー アプリの天気予報コントローラー (`Controllers/WeatherForecastController.cs`) で、`WeatherForecastController` に対する既存のルート (`[Route("[controller]")]`) を次のルートに置き換えます。

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  前にサーバー アプリの `Startup.Configure` メソッドに追加したミドルウェアでは、`/WeatherForecast` に対する受信要求が、ポート (5001/5002) またはドメイン (`firstapp.com`/`secondapp.com`) に応じて、`/FirstApp/WeatherForecast` または `/SecondApp/WeatherForecast` に変更されます。 前のコントローラー ルートは、サーバー アプリからクライアント アプリに気象データを返すために必要です。

### <a name="static-assets-and-class-libraries"></a>静的アセットとクラス ライブラリ

静的アセットには、次の方法を使用します。

* アセットがクライアント アプリの `wwwroot` フォルダー内にある場合は、通常どおりにパスを指定します。

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* アセットが [Razor クラス ライブラリ (RCL)](xref:blazor/components/class-libraries) の `wwwroot` フォルダーにある場合は、[RCL の記事](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)のガイダンスに従って、クライアント アプリの静的アセットを参照します。

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。 いずれかのコンポーネントでスタイルシートまたは JavaScript ファイルが必要な場合は、次のいずれかの方法を使用して、静的アセットを取得します。

* クライアント アプリの `wwwroot/index.html` ファイルでは、静的アセットに (`<link>`) リンクできます。
* コンポーネントでは、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、静的アセットを取得できます。

次の例では上記の方法を示します。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。 スタイルシートまたは JavaScript ファイルが必要なコンポーネントがある場合は、クライアント アプリの `wwwroot/index.html` ファイルに正しい静的アセットのリンクを含める必要があります。 次の例ではこれらの方法を示します。

::: moniker-end

次の `Jeep` コンポーネントをクライアント アプリのいずれかに追加します。 `Jeep` コンポーネントでは次のものが使用されています。

* クライアント アプリの `wwwroot` フォルダーにある画像 (`jeep-cj.png`)。
* [追加された Razor コンポーネント ライブラリ](xref:blazor/components/class-libraries) (`JeepImage`) の `wwwroot` フォルダーにある画像 (`jeep-yj.png`)。
* この例のコンポーネント (`Component1`) は、`JeepImage` ライブラリがソリューションに追加されると、RCL プロジェクト テンプレートによって自動的に作成されます。

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> 画像を所有している場合を除き、車両の画像を公開**しないでください**。 そうしないと、著作権侵害のリスクがあります。

::: moniker range=">= aspnetcore-5.0"

ライブラリの `jeep-yj.png` 画像を、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。 `my-component` CSS クラスをクライアント アプリのページに提供するには、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、ライブラリのスタイルシートにリンクします。

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

[`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用する代わりに、クライアント アプリの `wwwroot/index.html` ファイルからスタイルシートを読み込むこともできます。 この方法を使用すると、クライアント アプリのすべてのコンポーネントでスタイルシートを使用できるようになります。

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

ライブラリの `jeep-yj.png` 画像は、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

クライアント アプリの `wwwroot/index.html` ファイルでは、次の `<link>` タグを追加してライブラリのスタイルシートを要求します。

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

クライアント アプリの `NavMenu` コンポーネント (`Shared/NavMenu.razor`) に、`Jeep` コンポーネントへのナビゲーションを追加します。

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

RCL の詳細については、以下を参照してください。

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>スタンドアロン展開

"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。 任意の静的ファイル サーバーで Blazor アプリを提供できます。

スタンドアロンのデプロイ アセットは、`/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。

スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。 現時点では、アプリをホストする Linux サーバー イメージは使用できません。 このシナリオを可能にするための取り組みが進行中です。

### <a name="iis"></a>IIS

IIS は、Blazor アプリ対応の静的ファイル サーバーです。 Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。

発行されたアセットは、`/bin/Release/{TARGET FRAMEWORK}/publish` フォルダーに作成されます。 `publish` フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。

#### <a name="webconfig"></a>web.config

Blazor プロジェクトが発行されると、`web.config` ファイルが以下の IIS 構成で作成されます。

* 各ファイル拡張子に対して設定される MIME の種類は次のとおりです。
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* 次の MIME の種類に対しては、HTTP 圧縮が有効にされます。
  * `application/octet-stream`
  * `application/wasm`
* URL Rewrite Module のルールが確立されます。
  * アプリの静的なアセットが存在するサブディレクトリ (`wwwroot/{PATH REQUESTED}`) が提供されます。
  * ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント (`wwwroot/index.html`) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。
  
#### <a name="use-a-custom-webconfig"></a>カスタム web.config を使用する

カスタムの `web.config` ファイルを使用するには、カスタムの `web.config` ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。

#### <a name="install-the-url-rewrite-module"></a>URL リライト モジュールをインストールする

[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。 このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。 モジュールは、IIS Web サイトからダウンロードする必要があります。 Web Platform Installer を使用してモジュールをインストールします。

1. ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。 英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。 その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。
1. インストーラーをサーバーにコピーします。 インストーラーを実行します。 **[インストール]** ボタンを選択して、ライセンス条項に同意します。 インストールが完了した後、サーバーの再起動は必要はありません。

#### <a name="configure-the-website"></a>Web サイトを構成する

Web サイトの**物理パス**をアプリのフォルダーに設定します。 フォルダーには次のものが含まれます。

* `web.config` ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。
* アプリの静的なアセット フォルダー。

#### <a name="host-as-an-iis-sub-app"></a>IIS サブアプリとしてホストする

スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。

* 継承された ASP.NET Core モジュール ハンドラーを無効にします。

  Blazor アプリで発行された `web.config` ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:blazor/host-and-deploy/index#app-base-path)に加えて行われます。 IIS でサブアプリを構成するときに、アプリの `index.html` ファイル内のアプリのベース パスを、使用している IIS の別名に設定します。

#### <a name="brotli-and-gzip-compression"></a>Brotli と Gzip の圧縮

`web.config` を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。 構成例については、[`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) をご覧ください。

#### <a name="troubleshooting"></a>トラブルシューティング

Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。 モジュールがインストールされていない場合、IIS では `web.config` ファイルを解析できません。 これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。

IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。

### <a name="azure-storage"></a>Azure ストレージ

[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。 カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。

ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:

* **インデックス ドキュメント名**を `index.html` に設定します。
* **エラー ドキュメント パス**を `index.html` に設定します。 Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。 このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。 `index.html` BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。

ファイルの `Content-Type` ヘッダーに不適切な MIME の種類があるために、実行時にファイルが読み込まれない場合は、次のいずれかの操作を実行します。

* ファイルの展開時に適切な MIME の種類 (`Content-Type` ヘッダー) を設定するようにツールを構成します。
* アプリの展開後に、ファイルの MIME の種類 (`Content-Type` ヘッダー) を変更します。

  Storage Explorer (Azure portal) で、ファイルごとに次のようにします。
  
  1. ファイルを右クリックし、 **[プロパティ]** を選択します。
  1. **ContentType** を設定し、 **[保存]** ボタンを選択します。

詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。

### <a name="nginx"></a>Nginx

以下に示す `nginx.conf` ファイルは、Nginx が対応するファイルをディスク上で見つけられないときに `index.html` ファイルを送信するよう Nginx を構成する方法を示すために、簡略化されています。

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

[NGINX バースト レート制限](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)を [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) で設定するとき、アプリによって行われる比較的大量の要求を受け入れる目的で、場合によっては、Blazor WebAssembly アプリの `burst` パラメーター値を大きくする必要があります。 最初に、値を 60 以上に設定します。

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

"*503 - サービスを利用できません*" という状態コードを要求が受信していることがブラウザー開発者ツールまたはネットワーク トラフィック ツールで示されている場合、この値を増やします。

運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。

### <a name="nginx-in-docker"></a>Docker での Nginx

Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。 `nginx.config` ファイルをコンテナーにコピーするように、Dockerfile を更新します。

次の例に示すように、1 つの行を Dockerfile に追加します。

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:

1. Apache 構成ファイルを作成します。 次の例は、簡略化された構成ファイル (`blazorapp.config`) です。

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。

1. アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。

1. Apache サービスを再起動します。

詳細については、[`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) および [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) を参照してください。

### <a name="github-pages"></a>GitHub ページ

URL の書き換えを処理するために、`wwwroot/404.html` ファイルを、要求を `index.html` ページにリダイレクトするスクリプトと共に追加します。 例については、[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)を参照してください。

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [ライブ サイト](https://stevesandersonms.github.io/BlazorOnGitHubPages/)

組織のサイトではなくプロジェクトのサイトを使用しているときは、`wwwroot/index.html` 内の `<base>` タグを更新します。 `href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `/my-repository/`) に設定します。 [SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)では、[`.github/workflows/main.yml` 構成ファイル](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)による発行時に、基本 `href` が更新されます。

> [!NOTE]
> [SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)は、.NET Foundation または Microsoft では、所有、管理、またはサポートされていません。

## <a name="host-configuration-values"></a>ホストの構成値

[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。

### <a name="content-root"></a>コンテンツ ルート

`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。 次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>パス ベース

`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。 次の例では、`/relative-URL-path` はアプリのパス ベースです。 詳細については、「[アプリのベースパス](xref:blazor/host-and-deploy/index#app-base-path)」を参照してください。

> [!IMPORTANT]
> `<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。 `<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>トリマーを構成する

Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL トリミング設定が各リリース ビルド上で実行されます。 詳細については、「<xref:blazor/host-and-deploy/configure-trimmer>」を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>リンカーを構成する

Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。 詳細については、「<xref:blazor/host-and-deploy/configure-linker>」を参照してください。

::: moniker-end

## <a name="custom-boot-resource-loading"></a>カスタム ブート リソースの読み込み

Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。 次のシナリオで `loadBootResource` を使用します。

* ユーザーが、タイムゾーン データや `dotnet.wasm` などの静的なリソースを CDN から読み込むことができるようにする。
* HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。
* 各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。

`loadBootResource` パラメーターは次の表に表示されます。

| パラメーター    | 説明 |
| ------------ | ----------- |
| `type`       | リソースの型。 許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata` |
| `name`       | リソースの名前。 |
| `defaultUri` | リソースの相対 URI または絶対 URI。 |
| `integrity`  | 応答で予想されるコンテンツを表す整合性文字列。 |

`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。

* URI 文字列。 次の例 (`wwwroot/index.html`) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。

  * `dotnet.*.js`
  * `dotnet.wasm`
  * タイムゾーン データ

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。

  次の例 (`wwwroot/index.html`) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`。既定の読み込み動作になります。

外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。 通常、既定では、必要なヘッダーが CDN によって提供されます。

カスタム動作の型のみ指定する必要があります。 `loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。

## <a name="change-the-filename-extension-of-dll-files"></a>DLL ファイルのファイル名拡張子を変更する

アプリで発行されている `.dll` ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。

アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように `.dll` ファイルの名前を変更します。 アプリで発行された出力の `wwwroot` ディレクトリ (たとえば、`{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 内の `.dll` ファイルをターゲットにします。

次の例では、`.dll` ファイルの名前が `.bin` ファイル拡張子を使用するように変更されています。

Windows の場合:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Linux または macOS の場合:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
`.bin` とは異なるファイル拡張子を使用するには、前のコマンドで `.bin` を置き換えます。

圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルに対処するには、次のいずれかの方法を採用します。

* 圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルを削除します。 このアプローチでは、圧縮は無効になっています。
* 更新した `blazor.boot.json` ファイルを圧縮します。

上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。 `wwwroot/service-worker-assets.js.br` と `wwwroot/service-worker-assets.js.gz` を削除または再圧縮します。 そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。

次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> 同じアセンブリの名前変更と遅延読み込みについては、<xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> のガイダンスを参照してください。

フィードバックを提供するには、[aspnetcore/issue #5477](https://github.com/dotnet/aspnetcore/issues/5477) を参照してください。
