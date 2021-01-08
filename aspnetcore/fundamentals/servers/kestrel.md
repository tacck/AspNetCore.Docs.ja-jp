---
title: ASP.NET Core への Kestrel Web サーバーの実装
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5c9e1717ad603687343f015826a113e6945e4a41
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854614"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="65ed3-103">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="65ed3-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="65ed3-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="65ed3-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65ed3-105">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="65ed3-106">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="65ed3-107">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="65ed3-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="65ed3-108">HTTPS</span></span>
* <span data-ttu-id="65ed3-109">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="65ed3-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="65ed3-110">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="65ed3-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="65ed3-111">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="65ed3-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="65ed3-112">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="65ed3-113">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="65ed3-114">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65ed3-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="65ed3-115">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="65ed3-115">HTTP/2 support</span></span>

<span data-ttu-id="65ed3-116">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="65ed3-117">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="65ed3-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="65ed3-118">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="65ed3-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="65ed3-119">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="65ed3-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="65ed3-120">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="65ed3-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="65ed3-121">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="65ed3-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="65ed3-122">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="65ed3-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="65ed3-123">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="65ed3-124">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="65ed3-125">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="65ed3-126">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="65ed3-127">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="65ed3-128">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="65ed3-129">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="65ed3-130">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="65ed3-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="65ed3-131">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="65ed3-132">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="65ed3-133">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="65ed3-135">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-135">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="65ed3-137">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="65ed3-138">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="65ed3-139">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="65ed3-140">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="65ed3-141">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="65ed3-142">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="65ed3-142">A reverse proxy:</span></span>

* <span data-ttu-id="65ed3-143">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="65ed3-144">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="65ed3-145">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="65ed3-146">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="65ed3-147">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-148">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="65ed3-149">ASP.NET Core アプリでの Kestrel</span><span class="sxs-lookup"><span data-stu-id="65ed3-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="65ed3-150">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="65ed3-151">*Program.cs* では、<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> メソッドにより <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="65ed3-152">ホストのビルトに関する詳細については、「<xref:fundamentals/host/generic-host#set-up-a-host>」の「*ホストを設定する*」と「*既定の builder 設定*」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="65ed3-153">`ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="65ed3-154">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="65ed3-154">Kestrel options</span></span>

<span data-ttu-id="65ed3-155">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="65ed3-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="65ed3-157">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="65ed3-158"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="65ed3-159">この記事の後半で示す例では、Kestrel オプションが C# コードで構成されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="65ed3-160">Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="65ed3-161">たとえば、[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)によって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="65ed3-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> および[エンドポイント構成](#endpoint-configuration)は、構成プロバイダーから構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="65ed3-163">残りの Kestrel 構成は、C# コードで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="65ed3-164">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="65ed3-165">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="65ed3-166">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="65ed3-167">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="65ed3-168">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="65ed3-169">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="65ed3-170">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="65ed3-171">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="65ed3-172">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="65ed3-173">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="65ed3-174">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="65ed3-175">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="65ed3-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="65ed3-176">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="65ed3-177">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="65ed3-178">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="65ed3-179">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="65ed3-180">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="65ed3-181">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="65ed3-182">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="65ed3-183">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="65ed3-184">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="65ed3-185">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="65ed3-186">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="65ed3-187">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="65ed3-188">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="65ed3-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="65ed3-189">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="65ed3-190">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="65ed3-191">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="65ed3-192">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="65ed3-193">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="65ed3-194">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="65ed3-195">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="65ed3-196">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="65ed3-197">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="65ed3-198">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="65ed3-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="65ed3-200">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="65ed3-201">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="65ed3-202">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="65ed3-203">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="65ed3-204">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="65ed3-204">Maximum streams per connection</span></span>

<span data-ttu-id="65ed3-205">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="65ed3-206">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="65ed3-207">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="65ed3-208">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-208">Header table size</span></span>

<span data-ttu-id="65ed3-209">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="65ed3-210">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="65ed3-211">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="65ed3-212">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="65ed3-213">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-213">Maximum frame size</span></span>

<span data-ttu-id="65ed3-214">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="65ed3-215">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="65ed3-216">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="65ed3-217">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-217">Maximum request header size</span></span>

<span data-ttu-id="65ed3-218">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="65ed3-219">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="65ed3-220">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="65ed3-221">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="65ed3-222">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-222">Initial connection window size</span></span>

<span data-ttu-id="65ed3-223">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="65ed3-224">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="65ed3-225">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="65ed3-226">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="65ed3-227">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-227">Initial stream window size</span></span>

<span data-ttu-id="65ed3-228">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="65ed3-229">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="65ed3-230">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="65ed3-231">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="65ed3-232">HTTP/2 キープ アライブ ping 構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="65ed3-233">Kestrel は、接続されているクライアントに HTTP/2 ping を送信するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="65ed3-234">HTTP/2 ping は複数の目的で機能します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="65ed3-235">アイドル状態の接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-235">Keep idle connections alive.</span></span> <span data-ttu-id="65ed3-236">一部のクライアントとプロキシ サーバーでは、アイドル状態の接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="65ed3-237">HTTP/2 ping は接続におけるアクティビティと見なされ、接続がアイドル状態として閉じられるのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="65ed3-238">異常な接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-238">Close unhealthy connections.</span></span> <span data-ttu-id="65ed3-239">構成された時間でキープ アライブ ping にクライアントが応答しない接続はサーバーによって閉じられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="65ed3-240">HTTP/2 キープ アライブ ping に関連する 2 つの構成オプション:</span><span class="sxs-lookup"><span data-stu-id="65ed3-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="65ed3-241">`Http2.KeepAlivePingInterval` は ping を内部構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="65ed3-242">この時間内にフレームが受信されない場合、サーバーからクライアントにキープ アライブ ping が送信されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="65ed3-243">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ ping は無効になります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="65ed3-244">既定値は `TimeSpan.MaxValue` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="65ed3-245">`Http2.KeepAlivePingTimeout` は、ping タイムアウトを構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="65ed3-246">このタイムアウト内でサーバーが応答 ping など、いかなるフレームも受信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="65ed3-247">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ タイムアウトは無効になります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="65ed3-248">既定値は 20 秒です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="65ed3-249">予告編</span><span class="sxs-lookup"><span data-stu-id="65ed3-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="65ed3-250">Reset</span><span class="sxs-lookup"><span data-stu-id="65ed3-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="65ed3-251">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="65ed3-251">Synchronous I/O</span></span>

<span data-ttu-id="65ed3-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="65ed3-253">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-254">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="65ed3-255">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="65ed3-256">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="65ed3-257">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="65ed3-258">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-258">Endpoint configuration</span></span>

<span data-ttu-id="65ed3-259">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="65ed3-260">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="65ed3-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="65ed3-261">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-261">Specify URLs using the:</span></span>

* <span data-ttu-id="65ed3-262">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="65ed3-263">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="65ed3-264">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="65ed3-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="65ed3-265">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="65ed3-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="65ed3-266">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="65ed3-267">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="65ed3-268">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="65ed3-269">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-269">A development certificate is created:</span></span>

* <span data-ttu-id="65ed3-270">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="65ed3-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="65ed3-271">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="65ed3-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="65ed3-272">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="65ed3-273">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="65ed3-274"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="65ed3-275">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="65ed3-276">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="65ed3-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="65ed3-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="65ed3-278">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="65ed3-279">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="65ed3-280"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="65ed3-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="65ed3-282">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="65ed3-283">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="65ed3-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="65ed3-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="65ed3-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="65ed3-286">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="65ed3-287">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="65ed3-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="65ed3-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="65ed3-289">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="65ed3-290">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="65ed3-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="65ed3-291">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="65ed3-292">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-292">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="65ed3-293">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="65ed3-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="65ed3-294">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="65ed3-295">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="65ed3-296">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="65ed3-297">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="65ed3-298">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="65ed3-299">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="65ed3-300">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="65ed3-301">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="65ed3-302">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="65ed3-303">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="65ed3-304">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="65ed3-305">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-305">Supported configurations described next:</span></span>

* <span data-ttu-id="65ed3-306">構成なし</span><span class="sxs-lookup"><span data-stu-id="65ed3-306">No configuration</span></span>
* <span data-ttu-id="65ed3-307">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="65ed3-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="65ed3-308">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="65ed3-308">Change the defaults in code</span></span>

<span data-ttu-id="65ed3-309">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="65ed3-309">*No configuration*</span></span>

<span data-ttu-id="65ed3-310">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="65ed3-311">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="65ed3-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="65ed3-312">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="65ed3-313">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="65ed3-314">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="65ed3-315">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="65ed3-316">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="65ed3-317">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="65ed3-318">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="65ed3-319">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="65ed3-320">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="65ed3-320">Schema notes:</span></span>

* <span data-ttu-id="65ed3-321">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="65ed3-322">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="65ed3-323">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="65ed3-324">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="65ed3-325">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="65ed3-326">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="65ed3-327">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="65ed3-328">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="65ed3-329">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="65ed3-330">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="65ed3-331">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="65ed3-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="65ed3-333">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="65ed3-334">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="65ed3-335">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="65ed3-336">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="65ed3-337">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="65ed3-338">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="65ed3-339">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="65ed3-340">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="65ed3-340">*Change the defaults in code*</span></span>

<span data-ttu-id="65ed3-341">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="65ed3-342">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="65ed3-343">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="65ed3-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="65ed3-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="65ed3-345">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="65ed3-346">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="65ed3-347">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="65ed3-348">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="65ed3-349">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-349">SNI support requires:</span></span>

* <span data-ttu-id="65ed3-350">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="65ed3-351">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="65ed3-352">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="65ed3-353">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="65ed3-354">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="65ed3-355">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="65ed3-355">Connection logging</span></span>

<span data-ttu-id="65ed3-356">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="65ed3-357">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="65ed3-358">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="65ed3-359">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="65ed3-360">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-360">Bind to a TCP socket</span></span>

<span data-ttu-id="65ed3-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="65ed3-362">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="65ed3-363">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="65ed3-364">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-364">Bind to a Unix socket</span></span>

<span data-ttu-id="65ed3-365">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="65ed3-366">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="65ed3-367">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="65ed3-368">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="65ed3-369">ポート 0</span><span class="sxs-lookup"><span data-stu-id="65ed3-369">Port 0</span></span>

<span data-ttu-id="65ed3-370">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="65ed3-371">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="65ed3-372">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="65ed3-373">制限事項</span><span class="sxs-lookup"><span data-stu-id="65ed3-373">Limitations</span></span>

<span data-ttu-id="65ed3-374">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="65ed3-375">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="65ed3-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="65ed3-376">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="65ed3-376">`urls` host configuration key</span></span>
* <span data-ttu-id="65ed3-377">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="65ed3-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="65ed3-378">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="65ed3-379">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="65ed3-380">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="65ed3-381">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="65ed3-382">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-382">IIS endpoint configuration</span></span>

<span data-ttu-id="65ed3-383">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="65ed3-384">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="65ed3-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="65ed3-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="65ed3-386">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="65ed3-387">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="65ed3-388">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="65ed3-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="65ed3-389">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="65ed3-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="65ed3-390">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="65ed3-390">HTTP/1.1 only.</span></span> <span data-ttu-id="65ed3-391">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="65ed3-392">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="65ed3-392">HTTP/2 only.</span></span> <span data-ttu-id="65ed3-393">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="65ed3-394">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="65ed3-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="65ed3-395">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="65ed3-396">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="65ed3-397">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="65ed3-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="65ed3-398">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="65ed3-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="65ed3-399">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="65ed3-399">Renegotiation disabled</span></span>
* <span data-ttu-id="65ed3-400">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="65ed3-400">Compression disabled</span></span>
* <span data-ttu-id="65ed3-401">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="65ed3-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="65ed3-402">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="65ed3-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="65ed3-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="65ed3-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="65ed3-404">暗号スイートは禁止されない</span><span class="sxs-lookup"><span data-stu-id="65ed3-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="65ed3-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="65ed3-406">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="65ed3-407">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="65ed3-408">必要に応じて、接続ミドルウェアを使用し、特定の暗号のために接続ごとに TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="65ed3-409">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="65ed3-410">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="65ed3-411">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="65ed3-412">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="65ed3-413">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="65ed3-414">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="65ed3-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="65ed3-415">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="65ed3-416">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="65ed3-417">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="65ed3-418">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-418">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="65ed3-419">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="65ed3-419">URL prefixes</span></span>

<span data-ttu-id="65ed3-420">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-420">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="65ed3-421">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-421">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="65ed3-422">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-422">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="65ed3-423">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-423">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="65ed3-424">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-424">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="65ed3-425">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-425">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="65ed3-426">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-426">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="65ed3-427">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-427">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="65ed3-428">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-428">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="65ed3-429">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-429">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="65ed3-430">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-430">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="65ed3-431">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-431">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="65ed3-432">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-432">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="65ed3-433">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-433">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="65ed3-434">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-434">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="65ed3-435">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-435">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="65ed3-436">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="65ed3-436">Host filtering</span></span>

<span data-ttu-id="65ed3-437">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-437">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="65ed3-438">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-438">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="65ed3-439">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-439">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="65ed3-440">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-440">`Host` headers aren't validated.</span></span>

<span data-ttu-id="65ed3-441">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-441">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="65ed3-442">Host Filtering Middleware は、ASP.NET Core アプリに暗黙的に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-442">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="65ed3-443">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-443">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="65ed3-444">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-444">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="65ed3-445">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-445">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="65ed3-446">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-446">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="65ed3-447">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="65ed3-447">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="65ed3-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="65ed3-449">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-449">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="65ed3-450">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-450">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="65ed3-451">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-451">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="65ed3-452">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-452">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="65ed3-453">Libuv トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-453">Libuv transport configuration</span></span>

<span data-ttu-id="65ed3-454">ASP.NET Core 5.0 から、Kestrel の Libuv トランスポートは廃止されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-454">As of ASP.NET Core 5.0, Kestrel's Libuv transport is obsolete.</span></span> <span data-ttu-id="65ed3-455">Libuv トランスポートには、Windows ARM64 などの新しい OS プラットフォームをサポートする更新プログラムが提供されず、今後のリリースでは削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-455">Libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="65ed3-456">古い <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> メソッドの呼び出しをすべて削除し、代わりに Kestrel の既定の Socket トランスポートを使用してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-456">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="65ed3-457">Libuv トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-457">Libuv transport configuration</span></span>

<span data-ttu-id="65ed3-458">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>) を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="65ed3-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="65ed3-459">アプリのプロジェクト ファイルに [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="65ed3-460">`IWebHostBuilder` で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> を呼び出す</span><span class="sxs-lookup"><span data-stu-id="65ed3-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="65ed3-461">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-461">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="65ed3-462">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-462">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="65ed3-463">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-463">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="65ed3-464">HTTPS</span><span class="sxs-lookup"><span data-stu-id="65ed3-464">HTTPS</span></span>
* <span data-ttu-id="65ed3-465">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="65ed3-465">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="65ed3-466">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="65ed3-466">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="65ed3-467">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="65ed3-467">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="65ed3-468">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-468">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="65ed3-469">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-469">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="65ed3-470">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65ed3-470">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="65ed3-471">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="65ed3-471">HTTP/2 support</span></span>

<span data-ttu-id="65ed3-472">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="65ed3-473">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="65ed3-473">Operating system&dagger;</span></span>
  * <span data-ttu-id="65ed3-474">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="65ed3-474">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="65ed3-475">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="65ed3-475">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="65ed3-476">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="65ed3-476">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="65ed3-477">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="65ed3-477">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="65ed3-478">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="65ed3-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="65ed3-479">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-479">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="65ed3-480">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-480">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="65ed3-481">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-481">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="65ed3-482">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-482">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="65ed3-483">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="65ed3-484">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-484">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="65ed3-485">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-485">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="65ed3-486">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="65ed3-486">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="65ed3-487">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-487">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="65ed3-488">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-488">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="65ed3-489">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-489">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="65ed3-491">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-491">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="65ed3-493">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-493">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="65ed3-494">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-494">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="65ed3-495">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-495">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="65ed3-496">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-496">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="65ed3-497">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-497">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="65ed3-498">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="65ed3-498">A reverse proxy:</span></span>

* <span data-ttu-id="65ed3-499">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-499">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="65ed3-500">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-500">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="65ed3-501">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-501">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="65ed3-502">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-502">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="65ed3-503">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-503">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-504">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-504">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="65ed3-505">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="65ed3-505">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="65ed3-506">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-506">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="65ed3-507">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-507">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="65ed3-508">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-508">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="65ed3-509">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-509">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="65ed3-510">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-510">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="65ed3-511">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す **前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-511">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="65ed3-512">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="65ed3-512">Kestrel options</span></span>

<span data-ttu-id="65ed3-513">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-513">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="65ed3-514"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-514">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="65ed3-515">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-515">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="65ed3-516"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-516">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="65ed3-517">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-517">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="65ed3-518">たとえば、ファイル構成プロバイダーによって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-518">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="65ed3-519">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-519">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="65ed3-520">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-520">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="65ed3-521">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-521">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="65ed3-522">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-522">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="65ed3-523">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-523">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="65ed3-524">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-524">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="65ed3-525">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-525">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="65ed3-526">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-526">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="65ed3-527">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-527">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="65ed3-528">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-528">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="65ed3-529">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-529">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="65ed3-530">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="65ed3-530">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="65ed3-531">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-531">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="65ed3-532">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-532">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="65ed3-533">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-533">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="65ed3-534">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-534">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="65ed3-535">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-535">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="65ed3-536">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-536">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="65ed3-537">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-537">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="65ed3-538">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-538">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="65ed3-539">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-539">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="65ed3-540">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-540">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="65ed3-541">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-541">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="65ed3-542">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-542">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="65ed3-543">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="65ed3-543">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="65ed3-544">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-544">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="65ed3-545">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-545">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="65ed3-546">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-546">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="65ed3-547">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-547">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="65ed3-548">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-548">A minimum rate also applies to the response.</span></span> <span data-ttu-id="65ed3-549">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-549">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="65ed3-550">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-550">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="65ed3-551">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-551">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="65ed3-552">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-552">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="65ed3-553">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-553">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="65ed3-554">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-554">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="65ed3-555">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-555">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="65ed3-556">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-556">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="65ed3-557">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="65ed3-557">Maximum streams per connection</span></span>

<span data-ttu-id="65ed3-558">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-558">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="65ed3-559">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-559">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="65ed3-560">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-560">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="65ed3-561">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-561">Header table size</span></span>

<span data-ttu-id="65ed3-562">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-562">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="65ed3-563">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-563">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="65ed3-564">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-564">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="65ed3-565">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-565">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="65ed3-566">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-566">Maximum frame size</span></span>

<span data-ttu-id="65ed3-567">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-567">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="65ed3-568">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-568">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="65ed3-569">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-569">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="65ed3-570">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-570">Maximum request header size</span></span>

<span data-ttu-id="65ed3-571">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-571">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="65ed3-572">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-572">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="65ed3-573">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-573">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="65ed3-574">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-574">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="65ed3-575">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-575">Initial connection window size</span></span>

<span data-ttu-id="65ed3-576">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-576">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="65ed3-577">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-577">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="65ed3-578">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-578">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="65ed3-579">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-579">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="65ed3-580">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-580">Initial stream window size</span></span>

<span data-ttu-id="65ed3-581">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-581">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="65ed3-582">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-582">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="65ed3-583">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-583">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="65ed3-584">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-584">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="65ed3-585">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="65ed3-585">Synchronous I/O</span></span>

<span data-ttu-id="65ed3-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="65ed3-587">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-587">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-588">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-588">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="65ed3-589">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-589">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="65ed3-590">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-590">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="65ed3-591">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-591">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="65ed3-592">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-592">Endpoint configuration</span></span>

<span data-ttu-id="65ed3-593">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-593">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="65ed3-594">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="65ed3-594">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="65ed3-595">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-595">Specify URLs using the:</span></span>

* <span data-ttu-id="65ed3-596">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-596">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="65ed3-597">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-597">`--urls` command-line argument.</span></span>
* <span data-ttu-id="65ed3-598">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="65ed3-598">`urls` host configuration key.</span></span>
* <span data-ttu-id="65ed3-599">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="65ed3-599">`UseUrls` extension method.</span></span>

<span data-ttu-id="65ed3-600">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-600">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="65ed3-601">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-601">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="65ed3-602">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-602">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="65ed3-603">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-603">A development certificate is created:</span></span>

* <span data-ttu-id="65ed3-604">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="65ed3-604">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="65ed3-605">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="65ed3-605">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="65ed3-606">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-606">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="65ed3-607">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-607">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="65ed3-608"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-608">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="65ed3-609">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-609">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="65ed3-610">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="65ed3-610">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="65ed3-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="65ed3-612">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-612">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="65ed3-613">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-613">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="65ed3-614"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="65ed3-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="65ed3-616">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-616">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="65ed3-617">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-617">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="65ed3-618"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-618">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="65ed3-619">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="65ed3-619">Configure(IConfiguration)</span></span>

<span data-ttu-id="65ed3-620">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-620">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="65ed3-621">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-621">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="65ed3-622">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="65ed3-622">ListenOptions.UseHttps</span></span>

<span data-ttu-id="65ed3-623">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-623">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="65ed3-624">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="65ed3-624">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="65ed3-625">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-625">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="65ed3-626">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-626">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="65ed3-627">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="65ed3-627">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="65ed3-628">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-628">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="65ed3-629">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-629">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="65ed3-630">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-630">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="65ed3-631">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-631">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="65ed3-632">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-632">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="65ed3-633">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-633">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="65ed3-634">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-634">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="65ed3-635">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-635">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="65ed3-636">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-636">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="65ed3-637">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-637">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="65ed3-638">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-638">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="65ed3-639">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-639">Supported configurations described next:</span></span>

* <span data-ttu-id="65ed3-640">構成なし</span><span class="sxs-lookup"><span data-stu-id="65ed3-640">No configuration</span></span>
* <span data-ttu-id="65ed3-641">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="65ed3-641">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="65ed3-642">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="65ed3-642">Change the defaults in code</span></span>

<span data-ttu-id="65ed3-643">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="65ed3-643">*No configuration*</span></span>

<span data-ttu-id="65ed3-644">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-644">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="65ed3-645">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="65ed3-645">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="65ed3-646">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-646">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="65ed3-647">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-647">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="65ed3-648">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-648">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="65ed3-649">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-649">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="65ed3-650">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-650">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="65ed3-651">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-651">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="65ed3-652">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-652">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="65ed3-653">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-653">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="65ed3-654">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="65ed3-654">Schema notes:</span></span>

* <span data-ttu-id="65ed3-655">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-655">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="65ed3-656">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-656">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="65ed3-657">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-657">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="65ed3-658">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-658">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="65ed3-659">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-659">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="65ed3-660">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-660">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="65ed3-661">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-661">The `Certificate` section is optional.</span></span> <span data-ttu-id="65ed3-662">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-662">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="65ed3-663">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-663">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="65ed3-664">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-664">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="65ed3-665">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-665">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="65ed3-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="65ed3-667">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-667">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="65ed3-668">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-668">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="65ed3-669">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-669">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="65ed3-670">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-670">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="65ed3-671">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-671">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="65ed3-672">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-672">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="65ed3-673">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-673">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="65ed3-674">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="65ed3-674">*Change the defaults in code*</span></span>

<span data-ttu-id="65ed3-675">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-675">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="65ed3-676">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-676">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="65ed3-677">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="65ed3-677">*Kestrel support for SNI*</span></span>

<span data-ttu-id="65ed3-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="65ed3-679">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-679">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="65ed3-680">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-680">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="65ed3-681">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-681">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="65ed3-682">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-682">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="65ed3-683">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-683">SNI support requires:</span></span>

* <span data-ttu-id="65ed3-684">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-684">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="65ed3-685">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-685">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="65ed3-686">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-686">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="65ed3-687">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-687">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="65ed3-688">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-688">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="65ed3-689">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="65ed3-689">Connection logging</span></span>

<span data-ttu-id="65ed3-690">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-690">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="65ed3-691">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-691">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="65ed3-692">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-692">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="65ed3-693">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-693">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="65ed3-694">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-694">Bind to a TCP socket</span></span>

<span data-ttu-id="65ed3-695"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-695">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="65ed3-696">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-696">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="65ed3-697">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-697">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="65ed3-698">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-698">Bind to a Unix socket</span></span>

<span data-ttu-id="65ed3-699">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-699">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="65ed3-700">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-700">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="65ed3-701">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-701">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="65ed3-702">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-702">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="65ed3-703">ポート 0</span><span class="sxs-lookup"><span data-stu-id="65ed3-703">Port 0</span></span>

<span data-ttu-id="65ed3-704">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-704">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="65ed3-705">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-705">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="65ed3-706">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-706">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="65ed3-707">制限事項</span><span class="sxs-lookup"><span data-stu-id="65ed3-707">Limitations</span></span>

<span data-ttu-id="65ed3-708">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-708">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="65ed3-709">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="65ed3-709">`--urls` command-line argument</span></span>
* <span data-ttu-id="65ed3-710">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="65ed3-710">`urls` host configuration key</span></span>
* <span data-ttu-id="65ed3-711">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="65ed3-711">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="65ed3-712">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-712">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="65ed3-713">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-713">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="65ed3-714">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-714">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="65ed3-715">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-715">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="65ed3-716">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-716">IIS endpoint configuration</span></span>

<span data-ttu-id="65ed3-717">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-717">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="65ed3-718">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-718">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="65ed3-719">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="65ed3-719">ListenOptions.Protocols</span></span>

<span data-ttu-id="65ed3-720">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-720">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="65ed3-721">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-721">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="65ed3-722">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="65ed3-722">`HttpProtocols` enum value</span></span> | <span data-ttu-id="65ed3-723">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="65ed3-723">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="65ed3-724">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="65ed3-724">HTTP/1.1 only.</span></span> <span data-ttu-id="65ed3-725">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-725">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="65ed3-726">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="65ed3-726">HTTP/2 only.</span></span> <span data-ttu-id="65ed3-727">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-727">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="65ed3-728">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="65ed3-728">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="65ed3-729">HTTP/2 には、TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-729">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="65ed3-730">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-730">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="65ed3-731">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="65ed3-731">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="65ed3-732">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="65ed3-732">TLS version 1.2 or later</span></span>
* <span data-ttu-id="65ed3-733">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="65ed3-733">Renegotiation disabled</span></span>
* <span data-ttu-id="65ed3-734">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="65ed3-734">Compression disabled</span></span>
* <span data-ttu-id="65ed3-735">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="65ed3-735">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="65ed3-736">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="65ed3-736">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="65ed3-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="65ed3-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="65ed3-738">暗号スイートはブロックされない</span><span class="sxs-lookup"><span data-stu-id="65ed3-738">Cipher suite not blocked</span></span>

<span data-ttu-id="65ed3-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="65ed3-740">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-740">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="65ed3-741">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-741">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="65ed3-742">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-742">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="65ed3-743">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="65ed3-743">*Set the protocol from configuration*</span></span>

<span data-ttu-id="65ed3-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="65ed3-745">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-745">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="65ed3-746">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-746">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="65ed3-747">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-747">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="65ed3-748">Libuv トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-748">Libuv transport configuration</span></span>

<span data-ttu-id="65ed3-749">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="65ed3-749">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="65ed3-750">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-750">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="65ed3-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="65ed3-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="65ed3-752">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="65ed3-752">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="65ed3-753">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="65ed3-753">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="65ed3-754">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-754">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="65ed3-755"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-755">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="65ed3-756">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="65ed3-756">URL prefixes</span></span>

<span data-ttu-id="65ed3-757">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-757">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="65ed3-758">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-758">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="65ed3-759">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-759">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="65ed3-760">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-760">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="65ed3-761">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-761">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="65ed3-762">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-762">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="65ed3-763">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-763">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="65ed3-764">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-764">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="65ed3-765">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-765">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="65ed3-766">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-766">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="65ed3-767">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-767">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="65ed3-768">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-768">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="65ed3-769">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-769">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="65ed3-770">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-770">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="65ed3-771">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-771">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="65ed3-772">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-772">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="65ed3-773">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="65ed3-773">Host filtering</span></span>

<span data-ttu-id="65ed3-774">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-774">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="65ed3-775">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-775">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="65ed3-776">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-776">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="65ed3-777">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-777">`Host` headers aren't validated.</span></span>

<span data-ttu-id="65ed3-778">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-778">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="65ed3-779">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-779">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="65ed3-780">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-780">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="65ed3-781">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-781">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="65ed3-782">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-782">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="65ed3-783">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-783">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="65ed3-784">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="65ed3-784">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="65ed3-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="65ed3-786">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-786">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="65ed3-787">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-787">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="65ed3-788">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-788">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="65ed3-789">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-789">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="65ed3-790">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-790">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="65ed3-791">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-791">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="65ed3-792">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-792">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="65ed3-793">HTTPS</span><span class="sxs-lookup"><span data-stu-id="65ed3-793">HTTPS</span></span>
* <span data-ttu-id="65ed3-794">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="65ed3-794">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="65ed3-795">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="65ed3-795">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="65ed3-796">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-796">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="65ed3-797">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="65ed3-797">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="65ed3-798">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="65ed3-798">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="65ed3-799">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-799">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="65ed3-800">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-800">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="65ed3-801">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-801">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="65ed3-803">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="65ed3-803">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="65ed3-805">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-805">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="65ed3-806">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-806">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="65ed3-807">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-807">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="65ed3-808">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-808">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="65ed3-809">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-809">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="65ed3-810">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="65ed3-810">A reverse proxy:</span></span>

* <span data-ttu-id="65ed3-811">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-811">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="65ed3-812">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-812">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="65ed3-813">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-813">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="65ed3-814">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-814">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="65ed3-815">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-815">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-816">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-816">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="65ed3-817">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="65ed3-817">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="65ed3-818">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-818">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="65ed3-819">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-819">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="65ed3-820">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-820">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="65ed3-821">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-821">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="65ed3-822">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-822">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="65ed3-823">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="65ed3-823">Kestrel options</span></span>

<span data-ttu-id="65ed3-824">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-824">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="65ed3-825"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-825">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="65ed3-826">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-826">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="65ed3-827"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-827">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="65ed3-828">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-828">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="65ed3-829">たとえば、ファイル構成プロバイダーによって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-829">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="65ed3-830">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-830">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="65ed3-831">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-831">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="65ed3-832">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-832">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="65ed3-833">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-833">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="65ed3-834">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-834">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="65ed3-835">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="65ed3-835">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="65ed3-836">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-836">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="65ed3-837">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-837">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="65ed3-838">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-838">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="65ed3-839">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-839">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="65ed3-840">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-840">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="65ed3-841">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="65ed3-841">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="65ed3-842">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-842">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="65ed3-843">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-843">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="65ed3-844">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-844">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="65ed3-845">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-845">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="65ed3-846">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="65ed3-846">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="65ed3-847">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-847">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="65ed3-848">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-848">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="65ed3-849">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-849">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="65ed3-850">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-850">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="65ed3-851">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-851">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="65ed3-852">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-852">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="65ed3-853">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-853">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="65ed3-854">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="65ed3-854">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="65ed3-855">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-855">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="65ed3-856">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-856">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="65ed3-857">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-857">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="65ed3-858">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-858">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="65ed3-859">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-859">A minimum rate also applies to the response.</span></span> <span data-ttu-id="65ed3-860">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-860">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="65ed3-861">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-861">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="65ed3-862">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="65ed3-862">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="65ed3-863">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-863">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="65ed3-864">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-864">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="65ed3-865">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="65ed3-865">Synchronous I/O</span></span>

<span data-ttu-id="65ed3-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="65ed3-867">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-867">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="65ed3-868">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-868">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="65ed3-869">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-869">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="65ed3-870">同期 I/O を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-870">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="65ed3-871">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-871">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="65ed3-872">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-872">Endpoint configuration</span></span>

<span data-ttu-id="65ed3-873">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-873">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="65ed3-874">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="65ed3-874">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="65ed3-875">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-875">Specify URLs using the:</span></span>

* <span data-ttu-id="65ed3-876">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-876">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="65ed3-877">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="65ed3-877">`--urls` command-line argument.</span></span>
* <span data-ttu-id="65ed3-878">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="65ed3-878">`urls` host configuration key.</span></span>
* <span data-ttu-id="65ed3-879">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="65ed3-879">`UseUrls` extension method.</span></span>

<span data-ttu-id="65ed3-880">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-880">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="65ed3-881">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-881">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="65ed3-882">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-882">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="65ed3-883">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-883">A development certificate is created:</span></span>

* <span data-ttu-id="65ed3-884">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="65ed3-884">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="65ed3-885">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="65ed3-885">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="65ed3-886">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-886">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="65ed3-887">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-887">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="65ed3-888"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-888">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="65ed3-889">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-889">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="65ed3-890">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="65ed3-890">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="65ed3-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="65ed3-892">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-892">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="65ed3-893">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-893">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="65ed3-894"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="65ed3-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="65ed3-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="65ed3-896">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-896">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="65ed3-897">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-897">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="65ed3-898"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-898">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="65ed3-899">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="65ed3-899">Configure(IConfiguration)</span></span>

<span data-ttu-id="65ed3-900">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-900">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="65ed3-901">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-901">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="65ed3-902">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="65ed3-902">ListenOptions.UseHttps</span></span>

<span data-ttu-id="65ed3-903">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-903">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="65ed3-904">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="65ed3-904">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="65ed3-905">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-905">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="65ed3-906">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-906">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="65ed3-907">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="65ed3-907">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="65ed3-908">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-908">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="65ed3-909">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-909">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="65ed3-910">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-910">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="65ed3-911">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-911">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="65ed3-912">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-912">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="65ed3-913">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-913">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="65ed3-914">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-914">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="65ed3-915">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-915">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="65ed3-916">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-916">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="65ed3-917">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-917">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="65ed3-918">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-918">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="65ed3-919">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-919">Supported configurations described next:</span></span>

* <span data-ttu-id="65ed3-920">構成なし</span><span class="sxs-lookup"><span data-stu-id="65ed3-920">No configuration</span></span>
* <span data-ttu-id="65ed3-921">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="65ed3-921">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="65ed3-922">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="65ed3-922">Change the defaults in code</span></span>

<span data-ttu-id="65ed3-923">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="65ed3-923">*No configuration*</span></span>

<span data-ttu-id="65ed3-924">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-924">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="65ed3-925">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="65ed3-925">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="65ed3-926">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-926">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="65ed3-927">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-927">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="65ed3-928">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-928">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="65ed3-929">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-929">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="65ed3-930">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-930">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="65ed3-931">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-931">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="65ed3-932">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-932">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="65ed3-933">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-933">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="65ed3-934">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="65ed3-934">Schema notes:</span></span>

* <span data-ttu-id="65ed3-935">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-935">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="65ed3-936">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-936">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="65ed3-937">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-937">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="65ed3-938">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-938">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="65ed3-939">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-939">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="65ed3-940">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-940">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="65ed3-941">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-941">The `Certificate` section is optional.</span></span> <span data-ttu-id="65ed3-942">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-942">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="65ed3-943">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-943">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="65ed3-944">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-944">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="65ed3-945">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-945">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="65ed3-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="65ed3-947">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-947">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="65ed3-948">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-948">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="65ed3-949">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-949">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="65ed3-950">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-950">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="65ed3-951">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-951">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="65ed3-952">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-952">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="65ed3-953">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-953">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="65ed3-954">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="65ed3-954">*Change the defaults in code*</span></span>

<span data-ttu-id="65ed3-955">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-955">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="65ed3-956">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-956">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="65ed3-957">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="65ed3-957">*Kestrel support for SNI*</span></span>

<span data-ttu-id="65ed3-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="65ed3-959">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-959">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="65ed3-960">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-960">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="65ed3-961">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-961">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="65ed3-962">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-962">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="65ed3-963">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-963">SNI support requires:</span></span>

* <span data-ttu-id="65ed3-964">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-964">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="65ed3-965">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-965">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="65ed3-966">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-966">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="65ed3-967">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-967">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="65ed3-968">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-968">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="65ed3-969">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="65ed3-969">Connection logging</span></span>

<span data-ttu-id="65ed3-970">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-970">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="65ed3-971">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-971">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="65ed3-972">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-972">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="65ed3-973">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-973">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="65ed3-974">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-974">Bind to a TCP socket</span></span>

<span data-ttu-id="65ed3-975"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-975">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="65ed3-976">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-976">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="65ed3-977">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-977">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="65ed3-978">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="65ed3-978">Bind to a Unix socket</span></span>

<span data-ttu-id="65ed3-979">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-979">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="65ed3-980">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-980">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="65ed3-981">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-981">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="65ed3-982">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="65ed3-982">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="65ed3-983">ポート 0</span><span class="sxs-lookup"><span data-stu-id="65ed3-983">Port 0</span></span>

<span data-ttu-id="65ed3-984">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-984">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="65ed3-985">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-985">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="65ed3-986">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-986">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="65ed3-987">制限事項</span><span class="sxs-lookup"><span data-stu-id="65ed3-987">Limitations</span></span>

<span data-ttu-id="65ed3-988">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-988">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="65ed3-989">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="65ed3-989">`--urls` command-line argument</span></span>
* <span data-ttu-id="65ed3-990">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="65ed3-990">`urls` host configuration key</span></span>
* <span data-ttu-id="65ed3-991">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="65ed3-991">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="65ed3-992">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-992">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="65ed3-993">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-993">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="65ed3-994">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-994">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="65ed3-995">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-995">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="65ed3-996">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-996">IIS endpoint configuration</span></span>

<span data-ttu-id="65ed3-997">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-997">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="65ed3-998">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-998">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="65ed3-999">Libuv トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="65ed3-999">Libuv transport configuration</span></span>

<span data-ttu-id="65ed3-1000">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1000">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="65ed3-1001">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1001">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="65ed3-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="65ed3-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="65ed3-1003">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="65ed3-1003">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="65ed3-1004">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="65ed3-1004">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="65ed3-1005">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1005">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="65ed3-1006"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1006">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="65ed3-1007">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="65ed3-1007">URL prefixes</span></span>

<span data-ttu-id="65ed3-1008">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1008">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="65ed3-1009">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1009">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="65ed3-1010">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1010">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="65ed3-1011">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-1011">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="65ed3-1012">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1012">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="65ed3-1013">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-1013">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="65ed3-1014">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1014">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="65ed3-1015">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-1015">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="65ed3-1016">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1016">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="65ed3-1017">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1017">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="65ed3-1018">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1018">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="65ed3-1019">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1019">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="65ed3-1020">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="65ed3-1020">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="65ed3-1021">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1021">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="65ed3-1022">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1022">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="65ed3-1023">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1023">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="65ed3-1024">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="65ed3-1024">Host filtering</span></span>

<span data-ttu-id="65ed3-1025">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1025">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="65ed3-1026">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1026">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="65ed3-1027">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1027">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="65ed3-1028">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1028">`Host` headers aren't validated.</span></span>

<span data-ttu-id="65ed3-1029">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1029">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="65ed3-1030">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1030">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="65ed3-1031">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1031">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="65ed3-1032">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1032">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="65ed3-1033">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1033">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="65ed3-1034">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1034">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="65ed3-1035">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="65ed3-1035">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="65ed3-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="65ed3-1037">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1037">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="65ed3-1038">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1038">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="65ed3-1039">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1039">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="65ed3-1040">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1040">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="65ed3-1041">HTTP/1.1 要求のドレイン</span><span class="sxs-lookup"><span data-stu-id="65ed3-1041">HTTP/1.1 request draining</span></span>

<span data-ttu-id="65ed3-1042">HTTP 接続を開くには時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1042">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="65ed3-1043">HTTPS の場合も、リソースが大量に消費されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1043">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="65ed3-1044">そのため、Kestrel は、HTTP/1.1 プロトコルごとに接続を再利用しようとします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1044">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="65ed3-1045">接続を再利用できるようにするには、要求本文を完全に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1045">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="65ed3-1046">アプリは、サーバーがリダイレクトまたは 404 応答を返す `POST` 要求のように、常に要求本文を使用するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1046">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="65ed3-1047">`POST`-リダイレクトの場合:</span><span class="sxs-lookup"><span data-stu-id="65ed3-1047">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="65ed3-1048">クライアントが `POST` データの一部を既に送信している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1048">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="65ed3-1049">サーバーは 301 応答を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1049">The server writes the 301 response.</span></span>
* <span data-ttu-id="65ed3-1050">以前の要求本文の `POST` データが完全に読み取られるまで、新しい要求に対して接続を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1050">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="65ed3-1051">Kestrel は、要求本文のドレインを試行します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1051">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="65ed3-1052">要求本文のドレインは、データを処理せずに読み取りおよび破棄を行うことを意味します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1052">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="65ed3-1053">ドレイン プロセスでは、接続が再利用できるようになる代わりに、残りのデータをドレインする時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1053">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="65ed3-1054">ドレインのタイムアウトは 5 秒であり、この設定は構成できません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1054">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="65ed3-1055">`Content-Length` または `Transfer-Encoding` ヘッダーで指定されたすべてのデータがタイムアウト前に読み取られていない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1055">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="65ed3-1056">場合によっては、応答の書き込みの前後に要求をすぐに終了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1056">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="65ed3-1057">たとえば、クライアントのデータ キャップが制限されているため、アップロードするデータの制限が優先される場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1057">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="65ed3-1058">このような場合、要求を終了するには、コントローラー、Razor ページ、またはミドルウェアから [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1058">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="65ed3-1059">`Abort` を呼び出す際には、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1059">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="65ed3-1060">新しい接続の作成には、時間とコストがかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1060">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="65ed3-1061">接続が閉じる前にクライアントが応答を読み取ることは保証されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1061">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="65ed3-1062">`Abort` の呼び出しは最小限に抑え、一般的なエラーではなく重大なエラーが発生した場合のために予約する必要があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1062">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="65ed3-1063">`Abort` は特定の問題を解決する必要がある場合にのみ、呼び出してください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1063">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="65ed3-1064">たとえば、悪意のあるクライアントがデータを `POST` しようとしている場合や、クライアント コードに大量または多数の要求を引き起こすバグがある場合に `Abort` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1064">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="65ed3-1065">HTTP 404 (Not Found) などの一般的なエラー状況では、`Abort` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1065">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="65ed3-1066">`Abort` を呼び出す前に [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) を呼び出すと、サーバーが応答の書き込みを完了したことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1066">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="65ed3-1067">ただし、クライアントの動作は予測できないため、接続が中止される前に応答が読み取られない場合があります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1067">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="65ed3-1068">プロトコルでは、接続を閉じずに個々の要求ストリームを中止することがサポートされているため、HTTP/2 では、このプロセスが異なります。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1068">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="65ed3-1069">5 秒のドレイン タイムアウトは適用されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1069">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="65ed3-1070">応答の完了後に未読の要求本文データがある場合、サーバーは HTTP/2 RST フレームを送信します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1070">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="65ed3-1071">追加の要求本文データ フレームは無視されます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1071">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="65ed3-1072">可能なかぎり、クライアントが [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) の要求ヘッダーを利用し、要求本文の送信を開始する前にサーバーの応答を待機することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1072">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="65ed3-1073">これにより、クライアントは、不要なデータを送信する前に、応答を調べて中止することができます。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1073">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65ed3-1074">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="65ed3-1074">Additional resources</span></span>

* <span data-ttu-id="65ed3-1075">Linux で UNIX ソケットを使用している場合、アプリのシャットダウン時にソケットは自動的に削除されません。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1075">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="65ed3-1076">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/aspnetcore/issues/14134)します。</span><span class="sxs-lookup"><span data-stu-id="65ed3-1076">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="65ed3-1077">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="65ed3-1077">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
