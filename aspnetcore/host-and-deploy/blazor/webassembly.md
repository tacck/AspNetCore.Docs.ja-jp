---
title: "title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: guardrex description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239394"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>'Blazor'

'Identity'

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid: host-and-deploy/blazor/webassembly

ASP.NET Core Blazor WebAssembly をホストしてデプロイする

* 作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks) [Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。
* Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。 アプリがブラウザー UI スレッド上で直接実行されます。

## <a name="precompression"></a>次の展開戦略がサポートされています。

Blazor アプリは、ASP.NET Core アプリによって提供されます。 この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。

* Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。
* この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。

事前圧縮

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

Blazor WebAssembly アプリが公開されると、出力が事前圧縮されてアプリのサイズが縮小され、実行時の圧縮が不要になります。

## <a name="rewrite-urls-for-correct-routing"></a>次の圧縮アルゴリズムが使用されます。

[Brotli](https://tools.ietf.org/html/rfc7932) (最高レベル) [Gzip](https://tools.ietf.org/html/rfc1952)

* 圧縮を無効にするには、アプリケーションのプロジェクト ファイルに `BlazorEnableCompression` MSBuild プロパティを追加し、値を `false` に設定します。
* IIS の *web.config* の圧縮構成については、[「IIS」の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。

正しいルーティングのために URL を書き換える

1. Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。
1. 2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。
1. *Main.razor*:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。
1. *About.razor*: `About` コンポーネント。

アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合: ブラウザーにより要求が送信されます。 既定のページ (通常は *index.html*) が返されます。

*index.html* によりアプリがブートストラップされます。 Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。

Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。 " *Blazor WebAssembly アプリ*" 内にある内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。

要求は、ルーターによって内部的に処理されます。 ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。

## <a name="hosted-deployment-with-aspnet-core"></a>アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。

ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。

*index.html* が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。 IIS サーバーに展開する場合は、アプリの発行される *web.config* ファイルで URL Rewrite Module を使用できます。 詳細については、「[IIS](#iis)」セクションを参照してください。 ASP.NET Core でのホストされた展開

"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。

クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。

## <a name="standalone-deployment"></a>2 つのアプリが一緒に展開されます。

ASP.NET Core アプリをホストできる Web サーバーが必要です。 ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **[ホスト]** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。

ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。

### <a name="azure-app-service"></a>Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。

スタンドアロン展開

"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。 任意の静的ファイル サーバーで Blazor アプリを提供できます。 スタンドアロン展開のアセットは " */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot*" フォルダーに発行されます。

### <a name="iis"></a>Azure App Service

Blazor WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。 スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。

現時点では、アプリをホストする Linux サーバー イメージは使用できません。 このシナリオを可能にするための取り組みが進行中です。

#### <a name="webconfig"></a>IIS

IIS は、Blazor アプリ対応の静的ファイル サーバーです。

* Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。
  * 発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。
  * *publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。
  * web.config
  * Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。
  * 各ファイル拡張子に対して設定される MIME の種類は次のとおりです。
* *.dll*: `application/octet-stream`
  * `application/octet-stream`
  * `application/wasm`
* *.json*: `application/json`
  * *.wasm*: `application/wasm`
  * *.woff*: `application/font-woff`
  
#### <a name="use-a-custom-webconfig"></a>*.woff2*: `application/font-woff`

次の MIME の種類に対しては、HTTP 圧縮が有効にされます。

#### <a name="install-the-url-rewrite-module"></a>URL Rewrite Module のルールが確立されます。

アプリの静的なアセットが存在するサブディレクトリ ("*wwwroot/{要求されたパス}* ") が提供されます。 ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ("*wwwroot/index.html*") にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。 カスタム web.config を使用する カスタムの *web.config* ファイルを使用するには、カスタムの *web.config* ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。

1. URL リライト モジュールをインストールする [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。 このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。
1. モジュールは、IIS Web サイトからダウンロードする必要があります。 Web Platform Installer を使用してモジュールをインストールします。 ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。 英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。

#### <a name="configure-the-website"></a>その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。

インストーラーをサーバーにコピーします。 インストーラーを実行します。

* **[インストール]** ボタンを選択して、ライセンス条項に同意します。
* インストールが完了した後、サーバーの再起動は必要はありません。

#### <a name="host-as-an-iis-sub-app"></a>Web サイトを構成する

Web サイトの**物理パス**をアプリのフォルダーに設定します。

* フォルダーには次のものが含まれます。

  *web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* アプリの静的なアセット フォルダー。

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

IIS サブアプリとしてホストする スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。

#### <a name="brotli-and-gzip-compression"></a>継承された ASP.NET Core モジュール ハンドラーを無効にします。

Blazor アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。 `inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。

#### <a name="troubleshooting"></a>ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:host-and-deploy/blazor/index#app-base-path)に加えて行われます。

IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。 Brotli と Gzip の圧縮 *web.config* を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。

構成の例については、[web.config](webassembly/_samples/web.config?raw=true) を参照してください。

### <a name="azure-storage"></a>トラブルシューティング

Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。 モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。

これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。

* IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。
* Azure ストレージ [Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。 カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。 ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:

**インデックス ドキュメント名**を `index.html` に設定します。

* **エラー ドキュメント パス**を `index.html` に設定します。
* Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。

  このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。
  
  1. *index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。
  1. ファイルの `Content-Type` ヘッダーに不適切な MIME の種類があるために、実行時にファイルが読み込まれない場合は、次のいずれかの操作を実行します。

ファイルの展開時に適切な MIME の種類 (`Content-Type` ヘッダー) を設定するようにツールを構成します。

### <a name="nginx"></a>アプリの展開後に、ファイルの MIME の種類 (`Content-Type` ヘッダー) を変更します。

Storage Explorer (Azure portal) で、ファイルごとに次のようにします。

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

ファイルを右クリックし、 **[プロパティ]** を選択します。

### <a name="nginx-in-docker"></a>**ContentType** を設定し、 **[保存]** ボタンを選択します。

詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。 Nginx

以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。

Docker での Nginx

1. Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。 *nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。

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

1. 次の例に示すように、1 つの行を Dockerfile に追加します。

1. Apache

1. Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:

Apache 構成ファイルを作成します。

### <a name="github-pages"></a>次の例は、簡略化された構成ファイル (*blazorapp.config*) です。

Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。 アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。 Apache サービスを再起動します。

詳細については、「[mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)」と「[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)」を参照してください。 GitHub ページ

## <a name="host-configuration-values"></a>URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。

コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。

### <a name="content-root"></a>コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。

組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。 `href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。

* ホストの構成値 [Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* コンテンツ ルート `--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* 次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。 コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>アプリのディレクトリから、次のコマンドを実行します。

**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。 Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。

> [!IMPORTANT]
> Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。 パス ベース

* `--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。 次の例では、`/relative-URL-path` はアプリのパス ベースです。

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* 詳細については、「[アプリのベースパス](xref:host-and-deploy/blazor/index#app-base-path)」を参照してください。 `<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* `<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。 コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>アプリのディレクトリから、次のコマンドを実行します。

**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。

* この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。 Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。 URL

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* `--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。 コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>アプリのディレクトリから、次のコマンドを実行します。

**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。

## <a name="custom-boot-resource-loading"></a>Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。

Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。 リンカーを構成する

* Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。
* 詳細については、「<xref:host-and-deploy/blazor/configure-linker>」を参照してください。
* カスタム ブート リソースの読み込み

Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。

| 次のシナリオで `loadBootResource` を使用します。    | ユーザーが、タイムゾーン データや *dotnet.wasm* などの静的なリソースを CDN から読み込むことができるようにする。 |
| ------------ | ----------- |
| `type`       | HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。 各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。 |
| `name`       | `loadBootResource` パラメーターは次の表に表示されます。 |
| `defaultUri` | パラメーター |
| `integrity`  | 説明 |

リソースの型。

* 許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata` リソースの名前。

  * リソースの相対 URI または絶対 URI。
  * 応答で予想されるコンテンツを表す整合性文字列。
  * `loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。

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

* URI 文字列。 次の例 (*wwwroot/index.html*) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。

  *dotnet.\*.js*
  
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

* *dotnet.wasm*

タイムゾーン データ `Promise<Response>`。

ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。 次の例 (*wwwroot/index.html*) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。

## <a name="change-the-filename-extension-of-dll-files"></a>`null`/`undefined`。既定の読み込み動作になります。

外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。

通常、既定では、必要なヘッダーが CDN によって提供されます。 カスタム動作の型のみ指定する必要があります。

`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。

DLL ファイルのファイル名拡張子を変更する

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

アプリで発行されている " *.dll*" ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように " *.dll*" ファイルの名前を変更します。

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

アプリの発行された出力の "*wwwroot*" ディレクトリ内の " *.dll*" ファイルをターゲットにします (たとえば、" *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*")。

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
次の例では、" *.dll*" ファイルの名前が " *.bin*" ファイル拡張子を使用するように変更されています。

Windows の場合:

* サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。 Linux または macOS の場合:
* サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。

" *.bin*" とは異なるファイル拡張子を使用するには、前のコマンドで " *.bin*" を置き換えます。 圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルに対処するには、次のいずれかの方法を採用します。 圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルを削除します。

このアプローチでは、圧縮は無効になっています。

更新された "*blazor.boot.json*" ファイルを再圧縮します。

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

*wwwroot/service-worker-assets.js.br* と *wwwroot/service-worker-assets.js.gz* を削除または再圧縮します。

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。
 
