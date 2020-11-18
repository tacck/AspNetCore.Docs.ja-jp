---
title: Nginx 搭載の Linux で ASP.NET Core をホストする
author: rick-anderson
description: Ubuntu 16.04 でリバース プロキシとして Nginx をセットアップし、Kestrel で実行している ASP.NET Core Web アプリに HTTP トラフィックを転送する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: c4e0d70b41221f272bb4b1fe82cfa531ec6fcf15
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431067"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="9183c-103">Nginx 搭載の Linux で ASP.NET Core をホストする</span><span class="sxs-lookup"><span data-stu-id="9183c-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="9183c-104">[Sourabh Shirhatti](https://twitter.com/sshirhatti) による投稿</span><span class="sxs-lookup"><span data-stu-id="9183c-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="9183c-105">このガイドでは、Ubuntu 16.04 サーバーで本稼働対応の ASP.NET Core 環境をセットアップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9183c-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="9183c-106">これらの手順は、Ubuntu のより新しいバージョンで動作する可能性が高いですが、手順はまだ新しいバージョンでテストされていません。</span><span class="sxs-lookup"><span data-stu-id="9183c-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="9183c-107">ASP.NET Core でサポートされている他の Linux ディストリビューションについて詳しくは、「[Linux における .NET Core の前提条件](/dotnet/core/linux-prerequisites)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9183c-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="9183c-108">Ubuntu 14.04 の場合、Kestrel プロセスを監視するためのソリューションとして `supervisord` が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="9183c-109">`systemd` は Ubuntu 14.04 ではご利用いただけません。</span><span class="sxs-lookup"><span data-stu-id="9183c-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="9183c-110">Ubuntu 14.04 の手順については、[このトピックの前のバージョンを参照してください](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="9183c-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="9183c-111">このガイドでは:</span><span class="sxs-lookup"><span data-stu-id="9183c-111">This guide:</span></span>

* <span data-ttu-id="9183c-112">リバース プロキシ サーバーの背後に既存の ASP.NET Core アプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="9183c-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="9183c-113">Kestrel Web サーバーに要求を転送するようにリバース プロキシ サーバーを設定します。</span><span class="sxs-lookup"><span data-stu-id="9183c-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="9183c-114">Web アプリを起動時にデーモンとして実行します。</span><span class="sxs-lookup"><span data-stu-id="9183c-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="9183c-115">Web アプリの再起動に役立つようにプロセス管理ツールを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9183c-116">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9183c-116">Prerequisites</span></span>

1. <span data-ttu-id="9183c-117">Ubuntu 16.04 サーバーへのアクセスと sudo 特権が与えられた標準ユーザー アカウント。</span><span class="sxs-lookup"><span data-stu-id="9183c-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="9183c-118">サーバーへの .NET Core ランタイムのインストール。</span><span class="sxs-lookup"><span data-stu-id="9183c-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="9183c-119">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9183c-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="9183c-120">プレビューでない最新の .NET Core バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9183c-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="9183c-121">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** の下の表でプレビューでない最新のランタイムをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9183c-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="9183c-122">Linux の 「**パッケージ マネージャーの手順**」 リンクを選択し、ご利用の Ubuntu バージョン用の Ubuntu 手順に従います。</span><span class="sxs-lookup"><span data-stu-id="9183c-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="9183c-123">既存の ASP.NET Core アプリ。</span><span class="sxs-lookup"><span data-stu-id="9183c-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="9183c-124">共有フレームワークをアップグレードした後の任意の時点で、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="9183c-125">アプリを介して発行およびコピーする</span><span class="sxs-lookup"><span data-stu-id="9183c-125">Publish and copy over the app</span></span>

<span data-ttu-id="9183c-126">[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="9183c-127">アプリがローカル環境で実行されていて、セキュリティで保護された接続 (HTTPS) を行うように構成されていない場合は、次の方法のいずれかを採用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="9183c-128">セキュリティで保護されたローカル接続を処理するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="9183c-129">詳しくは、「[HTTPS の構成](#https-configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9183c-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="9183c-130">`Properties/launchSettings.json` ファイルの `applicationUrl` プロパティから `https://localhost:5001` を削除します (ある場合)。</span><span class="sxs-lookup"><span data-stu-id="9183c-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="9183c-131">開発環境から [dotnet publish](/dotnet/core/tools/dotnet-publish) を実行して、サーバー上で実行できるディレクトリ (例: `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`。このプレースホルダー `{TARGET FRAMEWORK MONIKER}` はターゲット フレームワーク モニカー/TFM です) にアプリをパッケージします。</span><span class="sxs-lookup"><span data-stu-id="9183c-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="9183c-132">サーバーで .NET Core ランタイムを管理しない場合、アプリは[独立した展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9183c-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="9183c-133">組織のワークフローに統合されているツール (`SCP`や `SFTP` など) を使用して、サーバーに ASP.NET Core アプリをコピーします。</span><span class="sxs-lookup"><span data-stu-id="9183c-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="9183c-134">Web アプリは一般的に `var` ディレクトリの下に配置されます (たとえば、`var/www/helloapp`)。</span><span class="sxs-lookup"><span data-stu-id="9183c-134">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="9183c-135">運用展開シナリオの場合、継続的インテグレーション ワークフローが、アプリの発行処理とサーバーへの資産のコピーを行います。</span><span class="sxs-lookup"><span data-stu-id="9183c-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="9183c-136">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9183c-136">Test the app:</span></span>

1. <span data-ttu-id="9183c-137">コマンド ラインから `dotnet <app_assembly>.dll` アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9183c-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="9183c-138">ブラウザーで、`http://<serveraddress>:<port>` に移動し、アプリが Linux のローカルで動作することを検証します。</span><span class="sxs-lookup"><span data-stu-id="9183c-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="9183c-139">リバース プロキシ サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="9183c-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="9183c-140">リバース プロキシは、動的 Web アプリを提供するための一般的な仕組みです。</span><span class="sxs-lookup"><span data-stu-id="9183c-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="9183c-141">リバース プロキシは HTTP 要求を終了させ、ASP.NET Core アプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="9183c-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="9183c-142">リバース プロキシ サーバーを利用する</span><span class="sxs-lookup"><span data-stu-id="9183c-142">Use a reverse proxy server</span></span>

<span data-ttu-id="9183c-143">Kestrel は、ASP.NET Core から動的なコンテンツを提供するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9183c-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="9183c-144">ただし、Web サーバーとしての機能は、IIS、Apache、Nginx などのサーバーと比べると制限されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="9183c-145">リバース プロキシ サーバーは、静的コンテンツ サービス、要求のキャッシュ、要求の圧縮、HTTP サーバーからの HTTPS 終了などの作業の負荷を軽減します。</span><span class="sxs-lookup"><span data-stu-id="9183c-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="9183c-146">リバース プロキシ サーバーは専用コンピューター上に置かれることもあれば、HTTP サーバーと並んで展開されることもあります。</span><span class="sxs-lookup"><span data-stu-id="9183c-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="9183c-147">このガイドの目的のために、単一インスタンスの Nginx が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="9183c-148">HTTP サーバーと並んで、同じサーバー上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="9183c-149">要件に応じて、別のセットアップを選択することも可能です。</span><span class="sxs-lookup"><span data-stu-id="9183c-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="9183c-150">要求はリバース プロキシによって転送されるため、[`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) パッケージの [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="9183c-151">リダイレクト URI とその他のセキュリティ ポリシーを正しく機能させるために、このミドルウェアは、`X-Forwarded-Proto` ヘッダーを利用して、`Request.Scheme` を更新します。</span><span class="sxs-lookup"><span data-stu-id="9183c-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="9183c-152">他のミドルウェアを呼び出す前に、`Startup.Configure` の一番上にある <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9183c-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="9183c-153">ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="9183c-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="9183c-154">ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合、転送される既定のヘッダーは `None` です。</span><span class="sxs-lookup"><span data-stu-id="9183c-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="9183c-155">標準 localhost アドレス (`127.0.0.1`) を含むループバック アドレス (`127.0.0.0/8`、`[::1]`) 上で実行されるプロキシは、既定で信頼されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-155">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="9183c-156">組織内のその他の信頼されているプロキシまたはネットワークによってインターネットと Web サーバーの間の要求が処理される場合は、それらを、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> を使用して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> のリストに追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="9183c-157">次の例では、IP アドレス 10.0.0.100 にある信頼されているプロキシ サーバーが `Startup.ConfigureServices` 内の Forwarded Headers Middleware `KnownProxies` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="9183c-158">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="9183c-159">Nginx をインストールする</span><span class="sxs-lookup"><span data-stu-id="9183c-159">Install Nginx</span></span>

<span data-ttu-id="9183c-160">`apt-get` を利用し、Nginx をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9183c-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="9183c-161">インストーラーにより `systemd` init スクリプトが作成されます。このスクリプトがシステム起動時に Nginx をデーモンとして実行します。</span><span class="sxs-lookup"><span data-stu-id="9183c-161">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="9183c-162">「[Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)」 (Nginx: 公式 Debian/Ubuntu パッケージ) にある Ubuntu 用のインストールの指示に従います。</span><span class="sxs-lookup"><span data-stu-id="9183c-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="9183c-163">オプションの Nginx モジュールが必要な場合、Nginx をソースからビルドする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="9183c-164">Nginx は初めてのインストールとなるので、次を実行して明示的に起動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="9183c-165">ブラウザーで Nginx の既定のランディング ページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9183c-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="9183c-166">ランディング ページは `http://<server_IP_address>/index.nginx-debian.html` からアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9183c-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="9183c-167">Nginx を構成する</span><span class="sxs-lookup"><span data-stu-id="9183c-167">Configure Nginx</span></span>

<span data-ttu-id="9183c-168">Nginx をリバース プロキシとして構成し、ASP.NET Core アプリに HTTP 要求を転送するには、`/etc/nginx/sites-available/default` を変更します。</span><span class="sxs-lookup"><span data-stu-id="9183c-168">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="9183c-169">テキスト エディターで開き、中身を次のものに変更します。</span><span class="sxs-lookup"><span data-stu-id="9183c-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="9183c-170">アプリが SignalR または Blazor Server アプリの場合、詳細については <xref:signalr/scale#linux-with-nginx> と <xref:blazor/host-and-deploy/server#linux-with-nginx> をそれぞれ参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-170">If the app is a SignalR or Blazor Server app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="9183c-171">`server_name` が一致しない場合、Nginx では既定のサーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="9183c-172">既定のサーバーが定義されていない場合、構成ファイルの最初のサーバーが既定のサーバーとなります。</span><span class="sxs-lookup"><span data-stu-id="9183c-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="9183c-173">ベスト プラクティスとして、構成ファイルで 444 の状態コードを返す既定のサーバーを具体的に追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="9183c-174">既定のサーバーの構成例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9183c-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="9183c-175">上記の構成ファイルと既定のサーバーでは、Nginx は、ホスト ヘッダー `example.com` または `*.example.com` で、ポート 80 でパブリック トラフィックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9183c-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="9183c-176">これらのホストと一致しない要求は、Kestrel に転送されません。</span><span class="sxs-lookup"><span data-stu-id="9183c-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="9183c-177">Nginx は一致する要求を Kestrel (`http://localhost:5000`) に転送します。</span><span class="sxs-lookup"><span data-stu-id="9183c-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="9183c-178">詳細については、「[How nginx processes a request](https://nginx.org/docs/http/request_processing.html)」(Nginx が要求を処理する方法) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9183c-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="9183c-179">Kestrel の IP/ポートを変更するには、[Kestrel のエンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="9183c-180">適切な [server_name directive](https://nginx.org/docs/http/server_names.html) を指定しないと、アプリにセキュリティ上の脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="9183c-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="9183c-181">親ドメイン全体を制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.example.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="9183c-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="9183c-182">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="9183c-183">Nginx の構成を確立したら、`sudo nginx -t` を実行して構成ファイルの構文を確認します。</span><span class="sxs-lookup"><span data-stu-id="9183c-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="9183c-184">構成ファイルがテストに合格したら、`sudo nginx -s reload` を実行することで、強制的に Nginx に変更を反映させます。</span><span class="sxs-lookup"><span data-stu-id="9183c-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="9183c-185">アプリをサーバーで直接実行するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="9183c-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="9183c-186">アプリのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="9183c-187">アプリを実行する:`dotnet <app_assembly.dll>`。ここで、`app_assembly.dll` はアプリのアセンブリ ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="9183c-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="9183c-188">アプリがサーバーで実行されているにも関わらず、インターネット上で応答がない場合、サーバーのファイアウォールを確認し、ポート 80 が開かれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9183c-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="9183c-189">Azure Ubuntu VM を使用している場合、受信ポート 80 のトラフィックを有効にするネットワーク セキュリティ グループ (NSG) 規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="9183c-190">送信トラフィックは、受信規則が有効になると自動的に生成されるので、送信ポート 80 規則を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9183c-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="9183c-191">アプリのテストが終了したら、コマンド プロンプトで `Ctrl+C` を使用してアプリをシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="9183c-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="9183c-192">アプリを監視する</span><span class="sxs-lookup"><span data-stu-id="9183c-192">Monitor the app</span></span>

<span data-ttu-id="9183c-193">サーバーは、`http://<serveraddress>:80` に対する要求を Kestrel で実行されている ASP.NET Core アプリ (`http://127.0.0.1:5000`) に転送するようにセットアップされました。</span><span class="sxs-lookup"><span data-stu-id="9183c-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="9183c-194">ただし、Nginx は Kestrel プロセスを管理するようには設定されていません。</span><span class="sxs-lookup"><span data-stu-id="9183c-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="9183c-195">`systemd` を使用してサービス ファイルを作成し、基になる Web アプリを起動して監視できます。</span><span class="sxs-lookup"><span data-stu-id="9183c-195">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="9183c-196">`systemd` は init システムであり、プロセスを起動、停止、管理するためのさまざまな高性能機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="9183c-196">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="9183c-197">サービス ファイルを作成する</span><span class="sxs-lookup"><span data-stu-id="9183c-197">Create the service file</span></span>

<span data-ttu-id="9183c-198">次のように、サービス定義ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="9183c-199">アプリのサービス ファイルの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9183c-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="9183c-200">前の例では、サービスを管理するユーザーは `User` オプションによって指定されています。</span><span class="sxs-lookup"><span data-stu-id="9183c-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="9183c-201">ユーザー (`www-data`) が存在し、アプリのファイルの適切な所有権を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="9183c-202">アプリが最初の割り込み信号を受信してからシャットダウンするのを待機する期間を構成するには、`TimeoutStopSec` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="9183c-203">この期間内にアプリがシャットダウンしない場合は、SIGKILL を発行してアプリを終了します。</span><span class="sxs-lookup"><span data-stu-id="9183c-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="9183c-204">タイムアウトを無効にするには、値として、単位なしの秒数 (`150` など)、期間の値 (`2min 30s` など)、または `infinity` を指定します。</span><span class="sxs-lookup"><span data-stu-id="9183c-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="9183c-205">`TimeoutStopSec` には、マネージャー構成ファイル (`systemd-system.conf`、`system.conf.d`、`systemd-user.conf`、`user.conf.d`) の `DefaultTimeoutStopSec` の値が既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="9183c-206">ほとんどのディストリビューションにおいて、タイムアウトの既定値は 90 秒となります。</span><span class="sxs-lookup"><span data-stu-id="9183c-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="9183c-207">Linux のファイル システムは大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="9183c-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="9183c-208">`ASPNETCORE_ENVIRONMENT` を `Production` に設定すると、`appsettings.production.json` ではなく、構成ファイル `appsettings.Production.json` が検索されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-208">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="9183c-209">構成プロバイダーが環境変数を読み取れるようにするために、一部の値 (たとえば SQL の接続文字列) をエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="9183c-210">次のコマンドを使用して、構成ファイルで使用するために適切にエスケープされた値を生成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9183c-211">コロン (`:`) 区切り記号は、環境変数の名前ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9183c-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="9183c-212">コロンの代わりに 2 つのアンダースコア (`__`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="9183c-213">環境変数が構成に読み取られるときに、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables)によって 2 つのアンダースコアがコロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="9183c-214">次の例では、接続文字列キー `ConnectionStrings:DefaultConnection` はサービス定義ファイルでは `ConnectionStrings__DefaultConnection` と設定されています。</span><span class="sxs-lookup"><span data-stu-id="9183c-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9183c-215">コロン (`:`) 区切り記号は、環境変数の名前ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9183c-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="9183c-216">コロンの代わりに 2 つのアンダースコア (`__`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="9183c-217">環境変数が構成に読み取られるときに、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)によって 2 つのアンダースコアがコロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="9183c-218">次の例では、接続文字列キー `ConnectionStrings:DefaultConnection` はサービス定義ファイルでは `ConnectionStrings__DefaultConnection` と設定されています。</span><span class="sxs-lookup"><span data-stu-id="9183c-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="9183c-219">ファイルを保存し、サービスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="9183c-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="9183c-220">サービスを起動し、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="9183c-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="9183c-221">構成されたリバース プロキシと `systemd` 経由で管理される Kestrel を使用して、Web アプリが完全に構成され、ローカル コンピューター上のブラウザーから `http://localhost` にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9183c-221">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="9183c-222">妨げとなるファイアウォールがなければ、リモート コンピューターからもアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9183c-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="9183c-223">応答ヘッダーを調べると、ASP.NET Core アプリが Kestrel によってサービス提供されていることが `Server` ヘッダーに示されています。</span><span class="sxs-lookup"><span data-stu-id="9183c-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="9183c-224">ログを表示する</span><span class="sxs-lookup"><span data-stu-id="9183c-224">View logs</span></span>

<span data-ttu-id="9183c-225">Kestrel を利用する Web アプリは `systemd` を使用して管理されるため、すべてのイベントとプロセスが記録され、中心的ジャーナルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="9183c-226">ただし、このジャーナルには、`systemd` が管理するすべてのサービスとプロセスのすべてのエントリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9183c-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="9183c-227">`kestrel-helloapp.service` 固有の項目を表示するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="9183c-228">さらに絞り込むには、時間オプションとして `--since today`、`--until 1 hour ago`、あるいはこの 2 つの組み合わせを利用すれば、返されるエントリの数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="9183c-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="9183c-229">データの保護</span><span class="sxs-lookup"><span data-stu-id="9183c-229">Data protection</span></span>

<span data-ttu-id="9183c-230">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証ミドルウェア (cookie ミドルウェアなど) やクロスサイト リクエスト フォージェリ (CSRF) 保護を含む、いくつかの ASP.NET Core [ ミドルウェア](xref:fundamentals/middleware/index)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="9183c-231">データ保護 API がユーザーのコードから呼び出されない場合でも、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="9183c-232">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="9183c-233">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="9183c-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="9183c-234">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="9183c-234">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="9183c-235">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="9183c-236">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="9183c-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="9183c-237">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9183c-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="9183c-238">キー リングを永続化して暗号化するようにデータ保護を構成する場合は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="9183c-239">要求ヘッダー フィールドが長すぎます</span><span class="sxs-lookup"><span data-stu-id="9183c-239">Long request header fields</span></span>

<span data-ttu-id="9183c-240">プロキシ サーバーの既定の設定では、通常、プラットフォームに応じて、要求ヘッダー フィールドが 4 K または 8 K に制限されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="9183c-241">アプリでは、既定値よりも長いフィールドが必要になる場合があります (たとえば、[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用するアプリ)。</span><span class="sxs-lookup"><span data-stu-id="9183c-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="9183c-242">長いフィールドが必要な場合は、プロキシ サーバーの既定の設定に対して調整が必要になります。</span><span class="sxs-lookup"><span data-stu-id="9183c-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="9183c-243">適用する値は、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="9183c-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="9183c-244">詳細については、ご利用のサーバーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="9183c-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="9183c-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="9183c-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="9183c-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="9183c-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="9183c-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="9183c-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="9183c-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="9183c-249">必要な場合を除いて、プロキシ バッファーの既定値を増やさないでください。</span><span class="sxs-lookup"><span data-stu-id="9183c-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="9183c-250">これらの値を増やすと、悪意のあるユーザーによるバッファー オーバーラン (オーバーフロー) とサービス拒否 (DoS) の攻撃のリスクが増加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="9183c-251">アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="9183c-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="9183c-252">AppArmor を有効にする</span><span class="sxs-lookup"><span data-stu-id="9183c-252">Enable AppArmor</span></span>

<span data-ttu-id="9183c-253">Linux Security Modules (LSM) は、Linux 2.6 以降の Linux カーネルに含まれるフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="9183c-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="9183c-254">LSM は、セキュリティ モジュールのさまざまな実装に対応しています。</span><span class="sxs-lookup"><span data-stu-id="9183c-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="9183c-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) は Mandatory Access Control システムを実装する LSM です。このシステムは、プログラムのリソース範囲を限定できます。</span><span class="sxs-lookup"><span data-stu-id="9183c-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="9183c-256">AppArmor が有効であり、正しく構成されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9183c-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="9183c-257">ファイアウォールを構成する</span><span class="sxs-lookup"><span data-stu-id="9183c-257">Configure the firewall</span></span>

<span data-ttu-id="9183c-258">使用されていないすべての外部ポートを閉じます。</span><span class="sxs-lookup"><span data-stu-id="9183c-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="9183c-259">Uncomplicated firewall (ufw) は `iptables` のフロント エンドとなり、ファイアウォールを構成するための CLI が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="9183c-260">ファイアウォールが正しく構成されていない場合、システム全体にアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="9183c-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="9183c-261">正しい SSH ポートを指定しないと、SSH を使用してシステムに接続する場合に、そのシステムから事実上閉め出されることになります。</span><span class="sxs-lookup"><span data-stu-id="9183c-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="9183c-262">既定のポートは 22 です。</span><span class="sxs-lookup"><span data-stu-id="9183c-262">The default port is 22.</span></span> <span data-ttu-id="9183c-263">詳細については、[ufw の概要](https://help.ubuntu.com/community/UFW)と[マニュアル](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="9183c-264">`ufw` をインストールし、必要なすべてのポートでトラフィックを許可するように構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="9183c-265">Nginx をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="9183c-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="9183c-266">Nginx 応答名を変更する</span><span class="sxs-lookup"><span data-stu-id="9183c-266">Change the Nginx response name</span></span>

<span data-ttu-id="9183c-267">`src/http/ngx_http_header_filter_module.c`を編集します:</span><span class="sxs-lookup"><span data-stu-id="9183c-267">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="9183c-268">構成オプション</span><span class="sxs-lookup"><span data-stu-id="9183c-268">Configure options</span></span>

<span data-ttu-id="9183c-269">必要なその他のモジュールでサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="9183c-270">アプリのセキュリティを強化するために、[ModSecurity](https://www.modsecurity.org/) のような Web アプリのファイアウォールの使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="9183c-271">HTTPS の構成</span><span class="sxs-lookup"><span data-stu-id="9183c-271">HTTPS configuration</span></span>

<span data-ttu-id="9183c-272">**セキュリティで保護された (HTTPS) ローカル接続用にアプリを構成する**</span><span class="sxs-lookup"><span data-stu-id="9183c-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="9183c-273">[dotnet run](/dotnet/core/tools/dotnet-run) コマンドでは、アプリの `Properties/launchSettings.json` ファイルが使用されます。このファイルでは、`applicationUrl` プロパティによって提供される URL でリッスンするように、アプリが構成されます (例: `https://localhost:5001;http://localhost:5000`)。</span><span class="sxs-lookup"><span data-stu-id="9183c-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's `Properties/launchSettings.json` file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="9183c-274">次のいずれかの方法を使用して、`dotnet run` コマンドまたは開発環境 (<kbd>F5</kbd>、Visual Studio Code では <kbd>Ctrl</kbd>+<kbd>F5</kbd>) の開発で、証明書を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="9183c-275">[構成から既定の証明書を置き換える](xref:fundamentals/servers/kestrel#configuration) (*推奨*)</span><span class="sxs-lookup"><span data-stu-id="9183c-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="9183c-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="9183c-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="9183c-277">**セキュリティで保護された (HTTPS) クライアント接続用にリバース プロキシを構成する**</span><span class="sxs-lookup"><span data-stu-id="9183c-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="9183c-278">信頼できる証明機関 (CA) が発行した、有効な証明書を指定することで、ポート `443` で HTTPS トラフィックを待ち受けるようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9183c-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="9183c-279">次の `/etc/nginx/nginx.conf` ファイルで示されているプラクティスの一部を採用することで、セキュリティを強化します。</span><span class="sxs-lookup"><span data-stu-id="9183c-279">Harden the security by employing some of the practices depicted in the following `/etc/nginx/nginx.conf` file.</span></span> <span data-ttu-id="9183c-280">たとえば、強力な暗号を選択したり、HTTP 経由のすべてのトラフィックを HTTPS にリダイレクトしたりします。</span><span class="sxs-lookup"><span data-stu-id="9183c-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="9183c-281">開発環境では、永続的なリダイレクト (301) ではなく、一時的なリダイレクト (302) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9183c-281">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="9183c-282">リンク キャッシュを使用すると、開発環境で不安定な動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-282">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="9183c-283">`HTTP Strict-Transport-Security` (HSTS) ヘッダーを追加すると、クライアントが行う後続のすべての要求が HTTPS 経由になります。</span><span class="sxs-lookup"><span data-stu-id="9183c-283">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="9183c-284">HSTS の重要なガイダンスについては、「<xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-284">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="9183c-285">今後 HTTPS を無効にする場合は、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="9183c-285">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="9183c-286">HSTS ヘッダーを追加しない</span><span class="sxs-lookup"><span data-stu-id="9183c-286">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="9183c-287">短い `max-age` 値を選択する</span><span class="sxs-lookup"><span data-stu-id="9183c-287">Choose a short `max-age` value.</span></span>

<span data-ttu-id="9183c-288">`/etc/nginx/proxy.conf` 構成ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-288">Add the `/etc/nginx/proxy.conf` configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="9183c-289">`/etc/nginx/nginx.conf` 構成ファイルの内容を、次のファイルに **置き換え** ます。</span><span class="sxs-lookup"><span data-stu-id="9183c-289">**Replace** the contents of the `/etc/nginx/nginx.conf` configuration file with the following file.</span></span> <span data-ttu-id="9183c-290">この例では、1 つの構成ファイルに `http` セクションと `server` セクションの両方が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9183c-290">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="9183c-291">アプリによって行われる大量の要求を受け入れる目的で、Blazor WebAssembly アプリの `burst` パラメーター値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9183c-291">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="9183c-292">詳細については、「<xref:blazor/host-and-deploy/webassembly#nginx>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9183c-292">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="9183c-293">Nginx をクリックジャッキングから守る</span><span class="sxs-lookup"><span data-stu-id="9183c-293">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="9183c-294">[クリックジャッキング](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)は "*UI 着せ替え攻撃*" とも呼ばれ、Web サイトの訪問者を騙して現在訪れているものとは異なるページのリンクやボタンをクリックさせる悪意のある攻撃です。</span><span class="sxs-lookup"><span data-stu-id="9183c-294">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="9183c-295">サイトをセキュリティで保護するには、`X-FRAME-OPTIONS` を使います。</span><span class="sxs-lookup"><span data-stu-id="9183c-295">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="9183c-296">クリックジャッキング攻撃を軽減するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="9183c-296">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="9183c-297">`nginx.conf` ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="9183c-297">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="9183c-298">行 `add_header X-Frame-Options "SAMEORIGIN";` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-298">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="9183c-299">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9183c-299">Save the file.</span></span>
1. <span data-ttu-id="9183c-300">Nginx を再起動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-300">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="9183c-301">MIME タイプ スニッフィング</span><span class="sxs-lookup"><span data-stu-id="9183c-301">MIME-type sniffing</span></span>

<span data-ttu-id="9183c-302">このヘッダーは応答コンテンツの種類をオーバーライドしないようにブラウザーに指示するので、ほとんどのブラウザーで MIME スニッフィングが阻止されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-302">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="9183c-303">`nosniff` オプションを指定すると、サーバーでのコンテンツが `text/html` の場合、ブラウザーでは `text/html` と表示されます。</span><span class="sxs-lookup"><span data-stu-id="9183c-303">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="9183c-304">`nginx.conf` ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="9183c-304">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="9183c-305">行 `add_header X-Content-Type-Options "nosniff";` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9183c-305">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="9183c-306">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9183c-306">Save the file.</span></span>
1. <span data-ttu-id="9183c-307">Nginx を再起動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-307">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="9183c-308">Nginx に関するその他の推奨事項</span><span class="sxs-lookup"><span data-stu-id="9183c-308">Additional Nginx suggestions</span></span>

<span data-ttu-id="9183c-309">サーバー上で共有フレームワークをアップグレードしたら、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="9183c-309">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9183c-310">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9183c-310">Additional resources</span></span>

* [<span data-ttu-id="9183c-311">Linux における .NET Core の前提条件</span><span class="sxs-lookup"><span data-stu-id="9183c-311">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="9183c-312">Nginx: バイナリ リリース: 公式 Debian/Ubuntu パッケージ</span><span class="sxs-lookup"><span data-stu-id="9183c-312">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="9183c-313">NGINX: 転送されるヘッダーの使用</span><span class="sxs-lookup"><span data-stu-id="9183c-313">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
