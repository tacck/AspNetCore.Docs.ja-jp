---
title: ASP.NET Core への Kestrel Web サーバーの実装
author: rick-anderson
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 56ac6635639eed93a84f47fc915c7013c6ed2381
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052332"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="59ed0-103">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="59ed0-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="59ed0-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="59ed0-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59ed0-105">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="59ed0-106">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="59ed0-107">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="59ed0-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="59ed0-108">HTTPS</span></span>
* <span data-ttu-id="59ed0-109">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="59ed0-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="59ed0-110">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="59ed0-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="59ed0-111">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="59ed0-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="59ed0-112">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="59ed0-113">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="59ed0-114">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="59ed0-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="59ed0-115">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="59ed0-115">HTTP/2 support</span></span>

<span data-ttu-id="59ed0-116">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59ed0-117">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="59ed0-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="59ed0-118">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="59ed0-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="59ed0-119">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="59ed0-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="59ed0-120">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="59ed0-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="59ed0-121">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="59ed0-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59ed0-122">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="59ed0-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59ed0-123">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="59ed0-124">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="59ed0-125">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="59ed0-126">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="59ed0-127">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="59ed0-128">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="59ed0-129">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="59ed0-130">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="59ed0-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="59ed0-131">Kestrel を単独で使用することも、 [インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、 [Nginx](https://nginx.org)、 [Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-131">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="59ed0-132">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="59ed0-133">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="59ed0-135">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-135">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="59ed0-137">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="59ed0-138">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="59ed0-139">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="59ed0-140">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="59ed0-141">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="59ed0-142">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="59ed0-142">A reverse proxy:</span></span>

* <span data-ttu-id="59ed0-143">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="59ed0-144">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="59ed0-145">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="59ed0-146">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="59ed0-147">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-148">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="59ed0-149">ASP.NET Core アプリでの Kestrel</span><span class="sxs-lookup"><span data-stu-id="59ed0-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="59ed0-150">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="59ed0-151">*Program.cs* では、<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> メソッドにより <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-151">In *Program.cs* , the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="59ed0-152">ホストのビルトに関する詳細については、「<xref:fundamentals/host/generic-host#set-up-a-host>」の「 *ホストを設定する* 」と「 *既定の builder 設定* 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="59ed0-153">`ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="59ed0-154">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="59ed0-154">Kestrel options</span></span>

<span data-ttu-id="59ed0-155">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="59ed0-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="59ed0-157">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="59ed0-158"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="59ed0-159">この記事の後半で示す例では、Kestrel オプションが C# コードで構成されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="59ed0-160">Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="59ed0-161">たとえば、 [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)によって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="59ed0-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> および[エンドポイント構成](#endpoint-configuration)は、構成プロバイダーから構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="59ed0-163">残りの Kestrel 構成は、C# コードで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="59ed0-164">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="59ed0-165">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="59ed0-166">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="59ed0-167">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="59ed0-168">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="59ed0-169">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="59ed0-170">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-170">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="59ed0-171">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="59ed0-172">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="59ed0-173">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="59ed0-174">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="59ed0-175">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="59ed0-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="59ed0-176">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="59ed0-177">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="59ed0-178">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="59ed0-179">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="59ed0-180">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="59ed0-181">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="59ed0-182">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59ed0-183">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="59ed0-184">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="59ed0-185">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="59ed0-186">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59ed0-187">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="59ed0-188">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="59ed0-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="59ed0-189">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="59ed0-190">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="59ed0-191">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="59ed0-192">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="59ed0-193">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="59ed0-194">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="59ed0-195">次の例では、 *Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-195">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="59ed0-196">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="59ed0-197">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="59ed0-198">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに " *すべて無効* " にできるためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="59ed0-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="59ed0-200">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="59ed0-201">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="59ed0-202">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="59ed0-203">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="59ed0-204">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="59ed0-204">Maximum streams per connection</span></span>

<span data-ttu-id="59ed0-205">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="59ed0-206">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="59ed0-207">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="59ed0-208">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-208">Header table size</span></span>

<span data-ttu-id="59ed0-209">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="59ed0-210">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="59ed0-211">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="59ed0-212">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="59ed0-213">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-213">Maximum frame size</span></span>

<span data-ttu-id="59ed0-214">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="59ed0-215">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="59ed0-216">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="59ed0-217">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-217">Maximum request header size</span></span>

<span data-ttu-id="59ed0-218">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="59ed0-219">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="59ed0-220">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="59ed0-221">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="59ed0-222">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-222">Initial connection window size</span></span>

<span data-ttu-id="59ed0-223">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="59ed0-224">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="59ed0-225">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="59ed0-226">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="59ed0-227">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-227">Initial stream window size</span></span>

<span data-ttu-id="59ed0-228">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="59ed0-229">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="59ed0-230">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="59ed0-231">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="59ed0-232">HTTP/2 キープ アライブ ping 構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="59ed0-233">Kestrel は、接続されているクライアントに HTTP/2 ping を送信するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="59ed0-234">HTTP/2 ping は複数の目的で機能します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="59ed0-235">アイドル状態の接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-235">Keep idle connections alive.</span></span> <span data-ttu-id="59ed0-236">一部のクライアントとプロキシ サーバーでは、アイドル状態の接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="59ed0-237">HTTP/2 ping は接続におけるアクティビティと見なされ、接続がアイドル状態として閉じられるのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="59ed0-238">異常な接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-238">Close unhealthy connections.</span></span> <span data-ttu-id="59ed0-239">構成された時間でキープ アライブ ping にクライアントが応答しない接続はサーバーによって閉じられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="59ed0-240">HTTP/2 キープ アライブ ping に関連する 2 つの構成オプション:</span><span class="sxs-lookup"><span data-stu-id="59ed0-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="59ed0-241">`Http2.KeepAlivePingInterval` は ping を内部構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="59ed0-242">この時間内にフレームが受信されない場合、サーバーからクライアントにキープ アライブ ping が送信されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="59ed0-243">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ ping は無効になります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="59ed0-244">既定値は `TimeSpan.MaxValue` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="59ed0-245">`Http2.KeepAlivePingTimeout` は、ping タイムアウトを構成する `TimeSpan` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="59ed0-246">このタイムアウト内でサーバーが応答 ping など、いかなるフレームも受信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="59ed0-247">このオプションが `TimeSpan.MaxValue` に設定されているとき、キープ アライブ タイムアウトは無効になります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="59ed0-248">既定値は 20 秒です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="59ed0-249">予告編</span><span class="sxs-lookup"><span data-stu-id="59ed0-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="59ed0-250">Reset</span><span class="sxs-lookup"><span data-stu-id="59ed0-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="59ed0-251">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="59ed0-251">Synchronous I/O</span></span>

<span data-ttu-id="59ed0-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="59ed0-253">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-254">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="59ed0-255">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="59ed0-256">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="59ed0-257">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="59ed0-258">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-258">Endpoint configuration</span></span>

<span data-ttu-id="59ed0-259">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="59ed0-260">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="59ed0-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="59ed0-261">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-261">Specify URLs using the:</span></span>

* <span data-ttu-id="59ed0-262">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="59ed0-263">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="59ed0-264">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="59ed0-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="59ed0-265">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="59ed0-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="59ed0-266">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="59ed0-267">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="59ed0-268">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="59ed0-269">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-269">A development certificate is created:</span></span>

* <span data-ttu-id="59ed0-270">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="59ed0-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="59ed0-271">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="59ed0-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="59ed0-272">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="59ed0-273">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="59ed0-274"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="59ed0-275">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="59ed0-276">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="59ed0-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="59ed0-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="59ed0-278">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="59ed0-279">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-280"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="59ed0-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="59ed0-282">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="59ed0-283">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-284"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="59ed0-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="59ed0-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="59ed0-286">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="59ed0-287">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="59ed0-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="59ed0-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="59ed0-289">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="59ed0-290">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="59ed0-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="59ed0-291">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="59ed0-292">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="59ed0-293">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="59ed0-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="59ed0-294">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="59ed0-295">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="59ed0-296">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="59ed0-297">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="59ed0-298">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="59ed0-299">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="59ed0-300">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="59ed0-301">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="59ed0-302">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="59ed0-303">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="59ed0-304">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="59ed0-305">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-305">Supported configurations described next:</span></span>

* <span data-ttu-id="59ed0-306">構成なし</span><span class="sxs-lookup"><span data-stu-id="59ed0-306">No configuration</span></span>
* <span data-ttu-id="59ed0-307">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="59ed0-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="59ed0-308">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="59ed0-308">Change the defaults in code</span></span>

<span data-ttu-id="59ed0-309">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="59ed0-309">*No configuration*</span></span>

<span data-ttu-id="59ed0-310">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="59ed0-311">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="59ed0-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="59ed0-312">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="59ed0-313">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="59ed0-314">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="59ed0-315">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="59ed0-316">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="59ed0-317">証明書 (後の例では **HttpsDefaultCert** ) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-317">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="59ed0-318">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="59ed0-319">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="59ed0-320">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="59ed0-320">Schema notes:</span></span>

* <span data-ttu-id="59ed0-321">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="59ed0-322">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="59ed0-323">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="59ed0-324">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="59ed0-325">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="59ed0-326">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="59ed0-327">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="59ed0-328">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="59ed0-329">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="59ed0-330">`Certificate` セクションは、 **Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="59ed0-331">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="59ed0-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="59ed0-333">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="59ed0-334">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="59ed0-335">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="59ed0-336">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="59ed0-337">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="59ed0-338">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="59ed0-339">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="59ed0-340">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="59ed0-340">*Change the defaults in code*</span></span>

<span data-ttu-id="59ed0-341">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="59ed0-342">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="59ed0-343">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="59ed0-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="59ed0-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="59ed0-345">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="59ed0-346">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="59ed0-347">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="59ed0-348">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="59ed0-349">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-349">SNI support requires:</span></span>

* <span data-ttu-id="59ed0-350">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="59ed0-351">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="59ed0-352">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="59ed0-353">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="59ed0-354">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="59ed0-355">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="59ed0-355">Connection logging</span></span>

<span data-ttu-id="59ed0-356">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="59ed0-357">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="59ed0-358">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="59ed0-359">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="59ed0-360">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-360">Bind to a TCP socket</span></span>

<span data-ttu-id="59ed0-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="59ed0-362">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="59ed0-363">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="59ed0-364">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-364">Bind to a Unix socket</span></span>

<span data-ttu-id="59ed0-365">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="59ed0-366">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="59ed0-367">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="59ed0-368">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="59ed0-369">ポート 0</span><span class="sxs-lookup"><span data-stu-id="59ed0-369">Port 0</span></span>

<span data-ttu-id="59ed0-370">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="59ed0-371">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="59ed0-372">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="59ed0-373">制限事項</span><span class="sxs-lookup"><span data-stu-id="59ed0-373">Limitations</span></span>

<span data-ttu-id="59ed0-374">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="59ed0-375">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="59ed0-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="59ed0-376">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="59ed0-376">`urls` host configuration key</span></span>
* <span data-ttu-id="59ed0-377">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="59ed0-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="59ed0-378">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="59ed0-379">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="59ed0-380">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="59ed0-381">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="59ed0-382">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-382">IIS endpoint configuration</span></span>

<span data-ttu-id="59ed0-383">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="59ed0-384">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="59ed0-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="59ed0-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="59ed0-386">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="59ed0-387">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="59ed0-388">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="59ed0-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="59ed0-389">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="59ed0-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="59ed0-390">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="59ed0-390">HTTP/1.1 only.</span></span> <span data-ttu-id="59ed0-391">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="59ed0-392">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="59ed0-392">HTTP/2 only.</span></span> <span data-ttu-id="59ed0-393">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="59ed0-394">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="59ed0-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="59ed0-395">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="59ed0-396">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="59ed0-397">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="59ed0-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="59ed0-398">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="59ed0-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="59ed0-399">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="59ed0-399">Renegotiation disabled</span></span>
* <span data-ttu-id="59ed0-400">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="59ed0-400">Compression disabled</span></span>
* <span data-ttu-id="59ed0-401">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="59ed0-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="59ed0-402">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="59ed0-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="59ed0-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="59ed0-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="59ed0-404">暗号スイートは禁止されない</span><span class="sxs-lookup"><span data-stu-id="59ed0-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="59ed0-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="59ed0-406">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="59ed0-407">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="59ed0-408">必要に応じて、接続ミドルウェアを使用し、特定の暗号のために接続ごとに TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="59ed0-409">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="59ed0-410">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="59ed0-411">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="59ed0-412">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="59ed0-413">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="59ed0-414">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="59ed0-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="59ed0-415">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="59ed0-416">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="59ed0-417">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="59ed0-418">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="59ed0-419">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-419">Transport configuration</span></span>

<span data-ttu-id="59ed0-420">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="59ed0-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="59ed0-421">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="59ed0-422">`IWebHostBuilder` で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す</span><span class="sxs-lookup"><span data-stu-id="59ed0-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="59ed0-423">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="59ed0-423">URL prefixes</span></span>

<span data-ttu-id="59ed0-424">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="59ed0-425">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="59ed0-426">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="59ed0-427">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="59ed0-428">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="59ed0-429">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="59ed0-430">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="59ed0-431">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="59ed0-432">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="59ed0-433">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="59ed0-434">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="59ed0-435">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="59ed0-436">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="59ed0-437">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="59ed0-438">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="59ed0-439">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="59ed0-440">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="59ed0-440">Host filtering</span></span>

<span data-ttu-id="59ed0-441">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="59ed0-442">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="59ed0-443">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="59ed0-444">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="59ed0-445">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="59ed0-446">Host Filtering Middleware は、ASP.NET Core アプリに暗黙的に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="59ed0-447">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="59ed0-448">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="59ed0-449">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="59ed0-450">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="59ed0-451">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="59ed0-451">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="59ed0-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="59ed0-453">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="59ed0-454">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="59ed0-455">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="59ed0-456">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="59ed0-457">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="59ed0-458">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="59ed0-459">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="59ed0-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="59ed0-460">HTTPS</span></span>
* <span data-ttu-id="59ed0-461">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="59ed0-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="59ed0-462">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="59ed0-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="59ed0-463">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="59ed0-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="59ed0-464">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="59ed0-465">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="59ed0-466">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="59ed0-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="59ed0-467">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="59ed0-467">HTTP/2 support</span></span>

<span data-ttu-id="59ed0-468">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59ed0-469">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="59ed0-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="59ed0-470">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="59ed0-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="59ed0-471">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="59ed0-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="59ed0-472">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="59ed0-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="59ed0-473">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="59ed0-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59ed0-474">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="59ed0-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59ed0-475">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="59ed0-476">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="59ed0-477">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="59ed0-478">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="59ed0-479">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="59ed0-480">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="59ed0-481">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="59ed0-482">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="59ed0-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="59ed0-483">Kestrel を単独で使用することも、 [インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、 [Nginx](https://nginx.org)、 [Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-483">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="59ed0-484">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="59ed0-485">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="59ed0-487">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-487">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="59ed0-489">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="59ed0-490">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="59ed0-491">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="59ed0-492">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="59ed0-493">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="59ed0-494">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="59ed0-494">A reverse proxy:</span></span>

* <span data-ttu-id="59ed0-495">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="59ed0-496">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="59ed0-497">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="59ed0-498">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="59ed0-499">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-500">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="59ed0-501">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="59ed0-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="59ed0-502">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="59ed0-503">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="59ed0-504">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-504">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="59ed0-505">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の " *ホストを設定する* " セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="59ed0-506">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="59ed0-507">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す **前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="59ed0-508">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="59ed0-508">Kestrel options</span></span>

<span data-ttu-id="59ed0-509">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="59ed0-510"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="59ed0-511">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="59ed0-512"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="59ed0-513">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="59ed0-514">たとえば、ファイル構成プロバイダーによって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="59ed0-515">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="59ed0-516">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="59ed0-517">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="59ed0-518">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="59ed0-519">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="59ed0-520">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="59ed0-521">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-521">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="59ed0-522">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="59ed0-523">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="59ed0-524">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="59ed0-525">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="59ed0-526">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="59ed0-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="59ed0-527">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="59ed0-528">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="59ed0-529">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="59ed0-530">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="59ed0-531">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="59ed0-532">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="59ed0-533">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59ed0-534">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="59ed0-535">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="59ed0-536">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="59ed0-537">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59ed0-538">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="59ed0-539">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="59ed0-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="59ed0-540">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="59ed0-541">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="59ed0-542">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="59ed0-543">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="59ed0-544">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="59ed0-545">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="59ed0-546">次の例では、 *Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-546">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="59ed0-547">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="59ed0-548">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="59ed0-549">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="59ed0-550">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="59ed0-551">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="59ed0-552">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="59ed0-553">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="59ed0-553">Maximum streams per connection</span></span>

<span data-ttu-id="59ed0-554">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="59ed0-555">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="59ed0-556">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="59ed0-557">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-557">Header table size</span></span>

<span data-ttu-id="59ed0-558">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="59ed0-559">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="59ed0-560">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="59ed0-561">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="59ed0-562">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-562">Maximum frame size</span></span>

<span data-ttu-id="59ed0-563">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="59ed0-564">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="59ed0-565">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="59ed0-566">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-566">Maximum request header size</span></span>

<span data-ttu-id="59ed0-567">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="59ed0-568">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="59ed0-569">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="59ed0-570">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="59ed0-571">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-571">Initial connection window size</span></span>

<span data-ttu-id="59ed0-572">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="59ed0-573">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="59ed0-574">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="59ed0-575">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="59ed0-576">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-576">Initial stream window size</span></span>

<span data-ttu-id="59ed0-577">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="59ed0-578">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="59ed0-579">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="59ed0-580">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="59ed0-581">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="59ed0-581">Synchronous I/O</span></span>

<span data-ttu-id="59ed0-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="59ed0-583">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-584">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="59ed0-585">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="59ed0-586">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="59ed0-587">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="59ed0-588">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-588">Endpoint configuration</span></span>

<span data-ttu-id="59ed0-589">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="59ed0-590">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="59ed0-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="59ed0-591">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-591">Specify URLs using the:</span></span>

* <span data-ttu-id="59ed0-592">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="59ed0-593">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="59ed0-594">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="59ed0-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="59ed0-595">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="59ed0-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="59ed0-596">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="59ed0-597">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="59ed0-598">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="59ed0-599">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-599">A development certificate is created:</span></span>

* <span data-ttu-id="59ed0-600">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="59ed0-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="59ed0-601">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="59ed0-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="59ed0-602">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="59ed0-603">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="59ed0-604"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="59ed0-605">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="59ed0-606">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="59ed0-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="59ed0-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="59ed0-608">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="59ed0-609">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-610"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="59ed0-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="59ed0-612">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="59ed0-613">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-614"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="59ed0-615">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="59ed0-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="59ed0-616">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="59ed0-617">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="59ed0-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="59ed0-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="59ed0-619">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="59ed0-620">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="59ed0-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="59ed0-621">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="59ed0-622">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-622">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="59ed0-623">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="59ed0-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="59ed0-624">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="59ed0-625">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="59ed0-626">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="59ed0-627">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="59ed0-628">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="59ed0-629">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="59ed0-630">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="59ed0-631">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="59ed0-632">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="59ed0-633">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="59ed0-634">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="59ed0-635">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-635">Supported configurations described next:</span></span>

* <span data-ttu-id="59ed0-636">構成なし</span><span class="sxs-lookup"><span data-stu-id="59ed0-636">No configuration</span></span>
* <span data-ttu-id="59ed0-637">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="59ed0-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="59ed0-638">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="59ed0-638">Change the defaults in code</span></span>

<span data-ttu-id="59ed0-639">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="59ed0-639">*No configuration*</span></span>

<span data-ttu-id="59ed0-640">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="59ed0-641">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="59ed0-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="59ed0-642">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="59ed0-643">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="59ed0-644">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="59ed0-645">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="59ed0-646">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="59ed0-647">証明書 (後の例では **HttpsDefaultCert** ) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-647">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="59ed0-648">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="59ed0-649">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="59ed0-650">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="59ed0-650">Schema notes:</span></span>

* <span data-ttu-id="59ed0-651">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="59ed0-652">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="59ed0-653">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="59ed0-654">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="59ed0-655">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="59ed0-656">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="59ed0-657">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="59ed0-658">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="59ed0-659">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="59ed0-660">`Certificate` セクションは、 **Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="59ed0-661">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="59ed0-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="59ed0-663">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="59ed0-664">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="59ed0-665">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="59ed0-666">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="59ed0-667">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="59ed0-668">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="59ed0-669">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="59ed0-670">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="59ed0-670">*Change the defaults in code*</span></span>

<span data-ttu-id="59ed0-671">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="59ed0-672">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="59ed0-673">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="59ed0-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="59ed0-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="59ed0-675">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="59ed0-676">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="59ed0-677">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="59ed0-678">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="59ed0-679">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-679">SNI support requires:</span></span>

* <span data-ttu-id="59ed0-680">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="59ed0-681">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="59ed0-682">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="59ed0-683">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="59ed0-684">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="59ed0-685">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="59ed0-685">Connection logging</span></span>

<span data-ttu-id="59ed0-686">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="59ed0-687">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="59ed0-688">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="59ed0-689">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="59ed0-690">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-690">Bind to a TCP socket</span></span>

<span data-ttu-id="59ed0-691"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="59ed0-692">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="59ed0-693">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="59ed0-694">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-694">Bind to a Unix socket</span></span>

<span data-ttu-id="59ed0-695">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="59ed0-696">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="59ed0-697">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="59ed0-698">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="59ed0-699">ポート 0</span><span class="sxs-lookup"><span data-stu-id="59ed0-699">Port 0</span></span>

<span data-ttu-id="59ed0-700">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="59ed0-701">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="59ed0-702">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="59ed0-703">制限事項</span><span class="sxs-lookup"><span data-stu-id="59ed0-703">Limitations</span></span>

<span data-ttu-id="59ed0-704">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="59ed0-705">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="59ed0-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="59ed0-706">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="59ed0-706">`urls` host configuration key</span></span>
* <span data-ttu-id="59ed0-707">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="59ed0-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="59ed0-708">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="59ed0-709">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="59ed0-710">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="59ed0-711">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="59ed0-712">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-712">IIS endpoint configuration</span></span>

<span data-ttu-id="59ed0-713">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="59ed0-714">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="59ed0-715">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="59ed0-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="59ed0-716">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="59ed0-717">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="59ed0-718">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="59ed0-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="59ed0-719">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="59ed0-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="59ed0-720">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="59ed0-720">HTTP/1.1 only.</span></span> <span data-ttu-id="59ed0-721">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="59ed0-722">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="59ed0-722">HTTP/2 only.</span></span> <span data-ttu-id="59ed0-723">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="59ed0-724">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="59ed0-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="59ed0-725">HTTP/2 には、TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="59ed0-726">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="59ed0-727">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="59ed0-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="59ed0-728">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="59ed0-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="59ed0-729">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="59ed0-729">Renegotiation disabled</span></span>
* <span data-ttu-id="59ed0-730">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="59ed0-730">Compression disabled</span></span>
* <span data-ttu-id="59ed0-731">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="59ed0-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="59ed0-732">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="59ed0-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="59ed0-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="59ed0-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="59ed0-734">暗号スイートはブロックされない</span><span class="sxs-lookup"><span data-stu-id="59ed0-734">Cipher suite not blocked</span></span>

<span data-ttu-id="59ed0-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="59ed0-736">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="59ed0-737">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-737">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="59ed0-738">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="59ed0-739">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="59ed0-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="59ed0-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="59ed0-741">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="59ed0-742">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="59ed0-743">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="59ed0-744">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-744">Transport configuration</span></span>

<span data-ttu-id="59ed0-745">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="59ed0-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="59ed0-746">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="59ed0-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="59ed0-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="59ed0-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="59ed0-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="59ed0-749">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="59ed0-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="59ed0-750">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="59ed0-751"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="59ed0-752">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="59ed0-752">URL prefixes</span></span>

<span data-ttu-id="59ed0-753">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="59ed0-754">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="59ed0-755">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="59ed0-756">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="59ed0-757">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="59ed0-758">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="59ed0-759">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="59ed0-760">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="59ed0-761">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="59ed0-762">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="59ed0-763">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="59ed0-764">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="59ed0-765">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="59ed0-766">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="59ed0-767">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="59ed0-768">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="59ed0-769">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="59ed0-769">Host filtering</span></span>

<span data-ttu-id="59ed0-770">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="59ed0-771">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="59ed0-772">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="59ed0-773">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="59ed0-774">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="59ed0-775">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="59ed0-776">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="59ed0-777">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="59ed0-778">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="59ed0-779">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="59ed0-780">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="59ed0-780">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="59ed0-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="59ed0-782">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="59ed0-783">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="59ed0-784">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="59ed0-785">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="59ed0-786">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="59ed0-787">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="59ed0-788">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="59ed0-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="59ed0-789">HTTPS</span></span>
* <span data-ttu-id="59ed0-790">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="59ed0-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="59ed0-791">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="59ed0-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="59ed0-792">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="59ed0-793">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="59ed0-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="59ed0-794">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="59ed0-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="59ed0-795">Kestrel を単独で使用することも、 [インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、 [Nginx](https://nginx.org)、 [Apache](https://httpd.apache.org/) などの *リバース プロキシ サーバー* と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-795">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="59ed0-796">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="59ed0-797">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="59ed0-799">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="59ed0-799">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="59ed0-801">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="59ed0-802">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="59ed0-803">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="59ed0-804">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="59ed0-805">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="59ed0-806">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="59ed0-806">A reverse proxy:</span></span>

* <span data-ttu-id="59ed0-807">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="59ed0-808">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="59ed0-809">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="59ed0-810">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="59ed0-811">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-812">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="59ed0-813">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="59ed0-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="59ed0-814">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="59ed0-815">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="59ed0-816">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-816">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="59ed0-817">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="59ed0-818">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の " *ホストを設定する* " セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="59ed0-819">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="59ed0-819">Kestrel options</span></span>

<span data-ttu-id="59ed0-820">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="59ed0-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="59ed0-822">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="59ed0-823"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="59ed0-824">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="59ed0-825">たとえば、ファイル構成プロバイダーによって、 *appsettings.json* または *appsettings.{Environment}.json* ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="59ed0-826">次の方法の **いずれか** を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="59ed0-827">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="59ed0-828">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="59ed0-829">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="59ed0-830">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="59ed0-831">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="59ed0-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="59ed0-832">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-832">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="59ed0-833">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="59ed0-834">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="59ed0-835">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="59ed0-836">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="59ed0-837">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="59ed0-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="59ed0-838">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="59ed0-839">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="59ed0-840">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="59ed0-841">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="59ed0-842">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="59ed0-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="59ed0-843">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="59ed0-844">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59ed0-845">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="59ed0-846">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="59ed0-847">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="59ed0-848">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59ed0-849">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="59ed0-850">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="59ed0-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="59ed0-851">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="59ed0-852">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="59ed0-853">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="59ed0-854">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="59ed0-855">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="59ed0-856">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="59ed0-857">次の例では、 *Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-857">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="59ed0-858">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="59ed0-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="59ed0-859">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="59ed0-860">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="59ed0-861">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="59ed0-861">Synchronous I/O</span></span>

<span data-ttu-id="59ed0-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="59ed0-863">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="59ed0-864">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="59ed0-865">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="59ed0-866">同期 I/O を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="59ed0-867">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="59ed0-868">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-868">Endpoint configuration</span></span>

<span data-ttu-id="59ed0-869">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="59ed0-870">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="59ed0-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="59ed0-871">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-871">Specify URLs using the:</span></span>

* <span data-ttu-id="59ed0-872">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="59ed0-873">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="59ed0-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="59ed0-874">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="59ed0-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="59ed0-875">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="59ed0-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="59ed0-876">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="59ed0-877">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="59ed0-878">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="59ed0-879">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-879">A development certificate is created:</span></span>

* <span data-ttu-id="59ed0-880">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="59ed0-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="59ed0-881">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="59ed0-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="59ed0-882">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="59ed0-883">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="59ed0-884"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="59ed0-885">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="59ed0-886">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="59ed0-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="59ed0-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="59ed0-888">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="59ed0-889">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-890"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="59ed0-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="59ed0-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="59ed0-892">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="59ed0-893">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="59ed0-894"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す **前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="59ed0-895">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="59ed0-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="59ed0-896">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="59ed0-897">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="59ed0-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="59ed0-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="59ed0-899">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="59ed0-900">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="59ed0-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="59ed0-901">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="59ed0-902">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-902">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="59ed0-903">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="59ed0-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="59ed0-904">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="59ed0-905">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="59ed0-906">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="59ed0-907">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="59ed0-908">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="59ed0-909">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="59ed0-910">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="59ed0-911">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="59ed0-912">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="59ed0-913">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="59ed0-914">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="59ed0-915">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-915">Supported configurations described next:</span></span>

* <span data-ttu-id="59ed0-916">構成なし</span><span class="sxs-lookup"><span data-stu-id="59ed0-916">No configuration</span></span>
* <span data-ttu-id="59ed0-917">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="59ed0-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="59ed0-918">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="59ed0-918">Change the defaults in code</span></span>

<span data-ttu-id="59ed0-919">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="59ed0-919">*No configuration*</span></span>

<span data-ttu-id="59ed0-920">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="59ed0-921">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="59ed0-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="59ed0-922">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="59ed0-923">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="59ed0-924">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="59ed0-925">次の *appsettings.json* の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="59ed0-926">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="59ed0-927">証明書 (後の例では **HttpsDefaultCert** ) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-927">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="59ed0-928">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="59ed0-929">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="59ed0-930">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="59ed0-930">Schema notes:</span></span>

* <span data-ttu-id="59ed0-931">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="59ed0-932">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="59ed0-933">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="59ed0-934">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="59ed0-935">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="59ed0-936">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="59ed0-937">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="59ed0-938">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="59ed0-939">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="59ed0-940">`Certificate` セクションは、 **Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="59ed0-941">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="59ed0-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="59ed0-943">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="59ed0-944">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="59ed0-945">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="59ed0-946">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="59ed0-947">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="59ed0-948">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="59ed0-949">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="59ed0-950">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="59ed0-950">*Change the defaults in code*</span></span>

<span data-ttu-id="59ed0-951">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="59ed0-952">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="59ed0-953">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="59ed0-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="59ed0-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="59ed0-955">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="59ed0-956">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="59ed0-957">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="59ed0-958">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="59ed0-959">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-959">SNI support requires:</span></span>

* <span data-ttu-id="59ed0-960">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="59ed0-961">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="59ed0-962">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="59ed0-963">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="59ed0-964">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="59ed0-965">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="59ed0-965">Connection logging</span></span>

<span data-ttu-id="59ed0-966">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="59ed0-967">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="59ed0-968">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="59ed0-969">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="59ed0-970">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-970">Bind to a TCP socket</span></span>

<span data-ttu-id="59ed0-971"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="59ed0-972">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="59ed0-973">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="59ed0-974">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="59ed0-974">Bind to a Unix socket</span></span>

<span data-ttu-id="59ed0-975">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="59ed0-976">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="59ed0-977">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="59ed0-978">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="59ed0-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="59ed0-979">ポート 0</span><span class="sxs-lookup"><span data-stu-id="59ed0-979">Port 0</span></span>

<span data-ttu-id="59ed0-980">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="59ed0-981">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="59ed0-982">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="59ed0-983">制限事項</span><span class="sxs-lookup"><span data-stu-id="59ed0-983">Limitations</span></span>

<span data-ttu-id="59ed0-984">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="59ed0-985">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="59ed0-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="59ed0-986">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="59ed0-986">`urls` host configuration key</span></span>
* <span data-ttu-id="59ed0-987">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="59ed0-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="59ed0-988">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="59ed0-989">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="59ed0-990">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="59ed0-991">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="59ed0-992">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-992">IIS endpoint configuration</span></span>

<span data-ttu-id="59ed0-993">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="59ed0-994">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="59ed0-995">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="59ed0-995">Transport configuration</span></span>

<span data-ttu-id="59ed0-996">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="59ed0-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="59ed0-997">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="59ed0-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="59ed0-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="59ed0-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="59ed0-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="59ed0-1000">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="59ed0-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="59ed0-1001">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="59ed0-1002"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="59ed0-1003">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="59ed0-1003">URL prefixes</span></span>

<span data-ttu-id="59ed0-1004">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="59ed0-1005">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="59ed0-1006">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="59ed0-1007">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="59ed0-1008">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="59ed0-1009">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="59ed0-1010">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="59ed0-1011">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="59ed0-1012">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="59ed0-1013">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="59ed0-1014">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="59ed0-1015">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="59ed0-1016">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="59ed0-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="59ed0-1017">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="59ed0-1018">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="59ed0-1019">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="59ed0-1020">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="59ed0-1020">Host filtering</span></span>

<span data-ttu-id="59ed0-1021">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="59ed0-1022">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="59ed0-1023">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="59ed0-1024">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="59ed0-1025">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="59ed0-1026">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="59ed0-1027">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="59ed0-1028">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="59ed0-1029">このミドルウェアを有効にするには、 *appsettings.json* /*appsettings.\<EnvironmentName>.json* で `AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="59ed0-1030">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="59ed0-1031">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="59ed0-1031">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="59ed0-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="59ed0-1033">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="59ed0-1034">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="59ed0-1035">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="59ed0-1036">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="59ed0-1037">HTTP/1.1 要求のドレイン</span><span class="sxs-lookup"><span data-stu-id="59ed0-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="59ed0-1038">HTTP 接続を開くには時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="59ed0-1039">HTTPS の場合も、リソースが大量に消費されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="59ed0-1040">そのため、Kestrel は、HTTP/1.1 プロトコルごとに接続を再利用しようとします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="59ed0-1041">接続を再利用できるようにするには、要求本文を完全に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="59ed0-1042">アプリは、サーバーがリダイレクトまたは 404 応答を返す `POST` 要求のように、常に要求本文を使用するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="59ed0-1043">`POST`-リダイレクトの場合:</span><span class="sxs-lookup"><span data-stu-id="59ed0-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="59ed0-1044">クライアントが `POST` データの一部を既に送信している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="59ed0-1045">サーバーは 301 応答を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="59ed0-1046">以前の要求本文の `POST` データが完全に読み取られるまで、新しい要求に対して接続を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="59ed0-1047">Kestrel は、要求本文のドレインを試行します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="59ed0-1048">要求本文のドレインは、データを処理せずに読み取りおよび破棄を行うことを意味します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="59ed0-1049">ドレイン プロセスでは、接続が再利用できるようになる代わりに、残りのデータをドレインする時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="59ed0-1050">ドレインのタイムアウトは 5 秒であり、この設定は構成できません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="59ed0-1051">`Content-Length` または `Transfer-Encoding` ヘッダーで指定されたすべてのデータがタイムアウト前に読み取られていない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="59ed0-1052">場合によっては、応答の書き込みの前後に要求をすぐに終了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="59ed0-1053">たとえば、クライアントのデータ キャップが制限されているため、アップロードするデータの制限が優先される場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="59ed0-1054">このような場合、要求を終了するには、コントローラー、Razor ページ、またはミドルウェアから [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="59ed0-1055">`Abort` を呼び出す際には、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="59ed0-1056">新しい接続の作成には、時間とコストがかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="59ed0-1057">接続が閉じる前にクライアントが応答を読み取ることは保証されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="59ed0-1058">`Abort` の呼び出しは最小限に抑え、一般的なエラーではなく重大なエラーが発生した場合のために予約する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="59ed0-1059">`Abort` は特定の問題を解決する必要がある場合にのみ、呼び出してください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="59ed0-1060">たとえば、悪意のあるクライアントがデータを `POST` しようとしている場合や、クライアント コードに大量または多数の要求を引き起こすバグがある場合に `Abort` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="59ed0-1061">HTTP 404 (Not Found) などの一般的なエラー状況では、`Abort` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="59ed0-1062">`Abort` を呼び出す前に [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) を呼び出すと、サーバーが応答の書き込みを完了したことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="59ed0-1063">ただし、クライアントの動作は予測できないため、接続が中止される前に応答が読み取られない場合があります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="59ed0-1064">プロトコルでは、接続を閉じずに個々の要求ストリームを中止することがサポートされているため、HTTP/2 では、このプロセスが異なります。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="59ed0-1065">5 秒のドレイン タイムアウトは適用されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="59ed0-1066">応答の完了後に未読の要求本文データがある場合、サーバーは HTTP/2 RST フレームを送信します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="59ed0-1067">追加の要求本文データ フレームは無視されます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="59ed0-1068">可能なかぎり、クライアントが [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) の要求ヘッダーを利用し、要求本文の送信を開始する前にサーバーの応答を待機することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="59ed0-1069">これにより、クライアントは、不要なデータを送信する前に、応答を調べて中止することができます。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59ed0-1070">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="59ed0-1070">Additional resources</span></span>

* <span data-ttu-id="59ed0-1071">Linux で UNIX ソケットを使用している場合、アプリのシャットダウン時にソケットは自動的に削除されません。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="59ed0-1072">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/aspnetcore/issues/14134)します。</span><span class="sxs-lookup"><span data-stu-id="59ed0-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="59ed0-1073">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="59ed0-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
