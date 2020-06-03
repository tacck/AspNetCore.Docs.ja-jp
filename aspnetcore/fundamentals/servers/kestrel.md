---
<span data-ttu-id="cf5fb-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-102">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-103">'Identity'</span></span>
- <span data-ttu-id="cf5fb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-105">'Razor'</span></span>
- <span data-ttu-id="cf5fb-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="cf5fb-107">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="cf5fb-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="cf5fb-108">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf5fb-109">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="cf5fb-110">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="cf5fb-111">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="cf5fb-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf5fb-112">HTTPS</span></span>
* <span data-ttu-id="cf5fb-113">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="cf5fb-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="cf5fb-114">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="cf5fb-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="cf5fb-115">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="cf5fb-116">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="cf5fb-117">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="cf5fb-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="cf5fb-119">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="cf5fb-119">HTTP/2 support</span></span>

<span data-ttu-id="cf5fb-120">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="cf5fb-121">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="cf5fb-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="cf5fb-122">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="cf5fb-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="cf5fb-123">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="cf5fb-124">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="cf5fb-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="cf5fb-125">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="cf5fb-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="cf5fb-126">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="cf5fb-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="cf5fb-127">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="cf5fb-128">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="cf5fb-129">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="cf5fb-130">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="cf5fb-131">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="cf5fb-132">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="cf5fb-133">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="cf5fb-134">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="cf5fb-135">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="cf5fb-136">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="cf5fb-137">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="cf5fb-139">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-139">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="cf5fb-141">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="cf5fb-142">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="cf5fb-143">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="cf5fb-144">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="cf5fb-145">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="cf5fb-146">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-146">A reverse proxy:</span></span>

* <span data-ttu-id="cf5fb-147">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="cf5fb-148">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="cf5fb-149">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="cf5fb-150">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="cf5fb-151">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-152">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="cf5fb-153">ASP.NET Core アプリでの Kestrel</span><span class="sxs-lookup"><span data-stu-id="cf5fb-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="cf5fb-154">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="cf5fb-155">*Program.cs* では、<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> メソッドにより <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="cf5fb-156">ホストのビルトに関する詳細については、「<xref:fundamentals/host/generic-host#set-up-a-host>」の「*ホストを設定する*」と「*既定の builder 設定*」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="cf5fb-157">`ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="cf5fb-158">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="cf5fb-158">Kestrel options</span></span>

<span data-ttu-id="cf5fb-159">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="cf5fb-160"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="cf5fb-161">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="cf5fb-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="cf5fb-163">この記事の後半で示す例では、Kestrel オプションが C# コードで構成されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="cf5fb-164">Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cf5fb-165">たとえば、[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)によって、"*appsettings.json*" または "*appsettings.{Environment}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="cf5fb-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> および[エンドポイント構成](#endpoint-configuration)は、構成プロバイダーから構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="cf5fb-167">残りの Kestrel 構成は、C# コードで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="cf5fb-168">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="cf5fb-169">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="cf5fb-170">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="cf5fb-171">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="cf5fb-172">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="cf5fb-173">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="cf5fb-174">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="cf5fb-175">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="cf5fb-176">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="cf5fb-177">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="cf5fb-178">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="cf5fb-179">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="cf5fb-180">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="cf5fb-181">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="cf5fb-182">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="cf5fb-183">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="cf5fb-184">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="cf5fb-185">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="cf5fb-186">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="cf5fb-187">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="cf5fb-188">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="cf5fb-189">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="cf5fb-190">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="cf5fb-191">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="cf5fb-192">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="cf5fb-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="cf5fb-193">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="cf5fb-194">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="cf5fb-195">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="cf5fb-196">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="cf5fb-197">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="cf5fb-198">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="cf5fb-199">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="cf5fb-200">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="cf5fb-201">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="cf5fb-202">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="cf5fb-203">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="cf5fb-204">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="cf5fb-205">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="cf5fb-206">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="cf5fb-207">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="cf5fb-208">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="cf5fb-208">Maximum streams per connection</span></span>

<span data-ttu-id="cf5fb-209">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="cf5fb-210">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="cf5fb-211">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="cf5fb-212">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-212">Header table size</span></span>

<span data-ttu-id="cf5fb-213">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="cf5fb-214">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="cf5fb-215">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="cf5fb-216">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="cf5fb-217">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-217">Maximum frame size</span></span>

<span data-ttu-id="cf5fb-218">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="cf5fb-219">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="cf5fb-220">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="cf5fb-221">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-221">Maximum request header size</span></span>

<span data-ttu-id="cf5fb-222">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="cf5fb-223">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="cf5fb-224">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="cf5fb-225">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="cf5fb-226">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-226">Initial connection window size</span></span>

<span data-ttu-id="cf5fb-227">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="cf5fb-228">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="cf5fb-229">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="cf5fb-230">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="cf5fb-231">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-231">Initial stream window size</span></span>

<span data-ttu-id="cf5fb-232">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="cf5fb-233">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="cf5fb-234">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="cf5fb-235">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="cf5fb-236">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="cf5fb-236">Synchronous I/O</span></span>

<span data-ttu-id="cf5fb-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="cf5fb-238">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-239">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="cf5fb-240">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="cf5fb-241">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="cf5fb-242">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="cf5fb-243">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-243">Endpoint configuration</span></span>

<span data-ttu-id="cf5fb-244">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="cf5fb-245">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="cf5fb-246">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-246">Specify URLs using the:</span></span>

* <span data-ttu-id="cf5fb-247">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="cf5fb-248">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="cf5fb-249">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="cf5fb-250">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="cf5fb-251">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="cf5fb-252">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="cf5fb-253">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="cf5fb-254">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-254">A development certificate is created:</span></span>

* <span data-ttu-id="cf5fb-255">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="cf5fb-256">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="cf5fb-257">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="cf5fb-258">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cf5fb-259"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="cf5fb-260">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="cf5fb-261">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="cf5fb-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="cf5fb-263">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="cf5fb-264">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-265"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="cf5fb-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="cf5fb-267">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="cf5fb-268">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-269"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="cf5fb-270">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="cf5fb-271">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="cf5fb-272">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="cf5fb-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="cf5fb-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="cf5fb-274">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="cf5fb-275">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="cf5fb-276">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="cf5fb-277">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-277">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="cf5fb-278">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="cf5fb-279">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="cf5fb-280">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="cf5fb-281">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="cf5fb-282">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="cf5fb-283">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="cf5fb-284">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="cf5fb-285">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="cf5fb-286">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="cf5fb-287">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="cf5fb-288">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="cf5fb-289">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="cf5fb-290">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-290">Supported configurations described next:</span></span>

* <span data-ttu-id="cf5fb-291">構成なし</span><span class="sxs-lookup"><span data-stu-id="cf5fb-291">No configuration</span></span>
* <span data-ttu-id="cf5fb-292">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="cf5fb-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="cf5fb-293">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="cf5fb-293">Change the defaults in code</span></span>

<span data-ttu-id="cf5fb-294">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-294">*No configuration*</span></span>

<span data-ttu-id="cf5fb-295">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="cf5fb-296">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="cf5fb-297">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="cf5fb-298">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="cf5fb-299">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="cf5fb-300">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="cf5fb-301">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="cf5fb-302">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="cf5fb-303">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="cf5fb-304">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="cf5fb-305">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-305">Schema notes:</span></span>

* <span data-ttu-id="cf5fb-306">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="cf5fb-307">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="cf5fb-308">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="cf5fb-309">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="cf5fb-310">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="cf5fb-311">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="cf5fb-312">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="cf5fb-313">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="cf5fb-314">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="cf5fb-315">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="cf5fb-316">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="cf5fb-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="cf5fb-318">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="cf5fb-319">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="cf5fb-320">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="cf5fb-321">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="cf5fb-322">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="cf5fb-323">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="cf5fb-324">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="cf5fb-325">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-325">*Change the defaults in code*</span></span>

<span data-ttu-id="cf5fb-326">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="cf5fb-327">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="cf5fb-328">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="cf5fb-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="cf5fb-330">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="cf5fb-331">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="cf5fb-332">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="cf5fb-333">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="cf5fb-334">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-334">SNI support requires:</span></span>

* <span data-ttu-id="cf5fb-335">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="cf5fb-336">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="cf5fb-337">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="cf5fb-338">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="cf5fb-339">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="cf5fb-340">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="cf5fb-340">Connection logging</span></span>

<span data-ttu-id="cf5fb-341">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="cf5fb-342">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="cf5fb-343">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="cf5fb-344">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="cf5fb-345">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-345">Bind to a TCP socket</span></span>

<span data-ttu-id="cf5fb-346"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="cf5fb-347">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="cf5fb-348">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="cf5fb-349">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-349">Bind to a Unix socket</span></span>

<span data-ttu-id="cf5fb-350">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="cf5fb-351">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="cf5fb-352">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="cf5fb-353">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="cf5fb-354">ポート 0</span><span class="sxs-lookup"><span data-stu-id="cf5fb-354">Port 0</span></span>

<span data-ttu-id="cf5fb-355">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="cf5fb-356">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="cf5fb-357">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="cf5fb-358">制限事項</span><span class="sxs-lookup"><span data-stu-id="cf5fb-358">Limitations</span></span>

<span data-ttu-id="cf5fb-359">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="cf5fb-360">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="cf5fb-361">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="cf5fb-361">`urls` host configuration key</span></span>
* <span data-ttu-id="cf5fb-362">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="cf5fb-363">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="cf5fb-364">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="cf5fb-365">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="cf5fb-366">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="cf5fb-367">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-367">IIS endpoint configuration</span></span>

<span data-ttu-id="cf5fb-368">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="cf5fb-369">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="cf5fb-370">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="cf5fb-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="cf5fb-371">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="cf5fb-372">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="cf5fb-373">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="cf5fb-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="cf5fb-374">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="cf5fb-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="cf5fb-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-376">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-377">'Identity'</span></span>
- <span data-ttu-id="cf5fb-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-379">'Razor'</span></span>
- <span data-ttu-id="cf5fb-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-382">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-383">'Identity'</span></span>
- <span data-ttu-id="cf5fb-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-385">'Razor'</span></span>
- <span data-ttu-id="cf5fb-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-388">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-389">'Identity'</span></span>
- <span data-ttu-id="cf5fb-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-391">'Razor'</span></span>
- <span data-ttu-id="cf5fb-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-394">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-395">'Identity'</span></span>
- <span data-ttu-id="cf5fb-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-397">'Razor'</span></span>
- <span data-ttu-id="cf5fb-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-400">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-401">'Identity'</span></span>
- <span data-ttu-id="cf5fb-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-403">'Razor'</span></span>
- <span data-ttu-id="cf5fb-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-406">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-407">'Identity'</span></span>
- <span data-ttu-id="cf5fb-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-409">'Razor'</span></span>
- <span data-ttu-id="cf5fb-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-412">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-413">'Identity'</span></span>
- <span data-ttu-id="cf5fb-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-415">'Razor'</span></span>
- <span data-ttu-id="cf5fb-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-418">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-419">'Identity'</span></span>
- <span data-ttu-id="cf5fb-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-421">'Razor'</span></span>
- <span data-ttu-id="cf5fb-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-424">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-425">'Identity'</span></span>
- <span data-ttu-id="cf5fb-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-427">'Razor'</span></span>
- <span data-ttu-id="cf5fb-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-430">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-431">'Identity'</span></span>
- <span data-ttu-id="cf5fb-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-433">'Razor'</span></span>
- <span data-ttu-id="cf5fb-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-436">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-437">'Identity'</span></span>
- <span data-ttu-id="cf5fb-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-439">'Razor'</span></span>
- <span data-ttu-id="cf5fb-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-440">'SignalR' uid:</span></span> 

<span data-ttu-id="cf5fb-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-442">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-443">'Identity'</span></span>
- <span data-ttu-id="cf5fb-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-445">'Razor'</span></span>
- <span data-ttu-id="cf5fb-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-448">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-449">'Identity'</span></span>
- <span data-ttu-id="cf5fb-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-451">'Razor'</span></span>
- <span data-ttu-id="cf5fb-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-454">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-455">'Identity'</span></span>
- <span data-ttu-id="cf5fb-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-457">'Razor'</span></span>
- <span data-ttu-id="cf5fb-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-460">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-461">'Identity'</span></span>
- <span data-ttu-id="cf5fb-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-463">'Razor'</span></span>
- <span data-ttu-id="cf5fb-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-466">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-467">'Identity'</span></span>
- <span data-ttu-id="cf5fb-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-469">'Razor'</span></span>
- <span data-ttu-id="cf5fb-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-472">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-473">'Identity'</span></span>
- <span data-ttu-id="cf5fb-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-475">'Razor'</span></span>
- <span data-ttu-id="cf5fb-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-478">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-479">'Identity'</span></span>
- <span data-ttu-id="cf5fb-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-481">'Razor'</span></span>
- <span data-ttu-id="cf5fb-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-484">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-485">'Identity'</span></span>
- <span data-ttu-id="cf5fb-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-487">'Razor'</span></span>
- <span data-ttu-id="cf5fb-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-490">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-491">'Identity'</span></span>
- <span data-ttu-id="cf5fb-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-493">'Razor'</span></span>
- <span data-ttu-id="cf5fb-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-496">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-497">'Identity'</span></span>
- <span data-ttu-id="cf5fb-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-499">'Razor'</span></span>
- <span data-ttu-id="cf5fb-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-502">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-503">'Identity'</span></span>
- <span data-ttu-id="cf5fb-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-505">'Razor'</span></span>
- <span data-ttu-id="cf5fb-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-508">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-509">'Identity'</span></span>
- <span data-ttu-id="cf5fb-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-511">'Razor'</span></span>
- <span data-ttu-id="cf5fb-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-512">'SignalR' uid:</span></span> 

<span data-ttu-id="cf5fb-513">--------------- | | `Http1`                    | HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="cf5fb-514">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-514">Can be used with or without TLS.</span></span> <span data-ttu-id="cf5fb-515">| | `Http2`                    | HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="cf5fb-516">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="cf5fb-517">| | `Http1AndHttp2`            | HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="cf5fb-518">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="cf5fb-519">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="cf5fb-520">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="cf5fb-521">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="cf5fb-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="cf5fb-522">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="cf5fb-522">Renegotiation disabled</span></span>
* <span data-ttu-id="cf5fb-523">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="cf5fb-523">Compression disabled</span></span>
* <span data-ttu-id="cf5fb-524">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="cf5fb-525">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="cf5fb-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="cf5fb-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="cf5fb-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="cf5fb-527">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="cf5fb-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="cf5fb-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="cf5fb-529">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="cf5fb-530">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="cf5fb-531">必要に応じて、接続ミドルウェアを使用し、特定の暗号のために接続ごとに TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="cf5fb-532">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="cf5fb-533">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="cf5fb-534">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="cf5fb-535">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="cf5fb-536">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="cf5fb-537">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="cf5fb-538">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="cf5fb-539">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="cf5fb-540">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="cf5fb-541">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="cf5fb-542">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-542">Transport configuration</span></span>

<span data-ttu-id="cf5fb-543">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="cf5fb-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="cf5fb-544">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="cf5fb-545">`IWebHostBuilder` で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す</span><span class="sxs-lookup"><span data-stu-id="cf5fb-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="cf5fb-546">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="cf5fb-546">URL prefixes</span></span>

<span data-ttu-id="cf5fb-547">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="cf5fb-548">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="cf5fb-549">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="cf5fb-550">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="cf5fb-551">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="cf5fb-552">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="cf5fb-553">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="cf5fb-554">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="cf5fb-555">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="cf5fb-556">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="cf5fb-557">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="cf5fb-558">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="cf5fb-559">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="cf5fb-560">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="cf5fb-561">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="cf5fb-562">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="cf5fb-563">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-563">Host filtering</span></span>

<span data-ttu-id="cf5fb-564">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="cf5fb-565">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="cf5fb-566">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="cf5fb-567">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="cf5fb-568">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="cf5fb-569">Host Filtering Middleware は、ASP.NET Core アプリに暗黙的に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="cf5fb-570">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="cf5fb-571">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="cf5fb-572">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<EnvironmentName>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="cf5fb-573">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="cf5fb-574">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="cf5fb-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="cf5fb-576">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="cf5fb-577">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="cf5fb-578">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="cf5fb-579">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="cf5fb-580">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="cf5fb-581">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="cf5fb-582">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="cf5fb-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf5fb-583">HTTPS</span></span>
* <span data-ttu-id="cf5fb-584">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="cf5fb-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="cf5fb-585">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="cf5fb-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="cf5fb-586">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="cf5fb-587">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="cf5fb-588">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="cf5fb-589">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="cf5fb-590">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="cf5fb-590">HTTP/2 support</span></span>

<span data-ttu-id="cf5fb-591">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="cf5fb-592">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="cf5fb-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="cf5fb-593">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="cf5fb-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="cf5fb-594">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="cf5fb-595">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="cf5fb-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="cf5fb-596">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="cf5fb-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="cf5fb-597">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="cf5fb-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="cf5fb-598">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="cf5fb-599">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="cf5fb-600">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="cf5fb-601">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="cf5fb-602">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="cf5fb-603">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="cf5fb-604">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="cf5fb-605">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="cf5fb-606">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="cf5fb-607">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="cf5fb-608">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="cf5fb-610">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-610">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="cf5fb-612">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="cf5fb-613">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="cf5fb-614">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="cf5fb-615">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="cf5fb-616">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="cf5fb-617">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-617">A reverse proxy:</span></span>

* <span data-ttu-id="cf5fb-618">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="cf5fb-619">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="cf5fb-620">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="cf5fb-621">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="cf5fb-622">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-623">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="cf5fb-624">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="cf5fb-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="cf5fb-625">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cf5fb-626">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="cf5fb-627">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="cf5fb-628">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="cf5fb-629">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="cf5fb-630">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す**前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="cf5fb-631">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="cf5fb-631">Kestrel options</span></span>

<span data-ttu-id="cf5fb-632">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="cf5fb-633"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="cf5fb-634">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="cf5fb-635"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="cf5fb-636">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cf5fb-637">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="cf5fb-638">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="cf5fb-639">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="cf5fb-640">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="cf5fb-641">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="cf5fb-642">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="cf5fb-643">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="cf5fb-644">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="cf5fb-645">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="cf5fb-646">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="cf5fb-647">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="cf5fb-648">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="cf5fb-649">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="cf5fb-650">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="cf5fb-651">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="cf5fb-652">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="cf5fb-653">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="cf5fb-654">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="cf5fb-655">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="cf5fb-656">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="cf5fb-657">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="cf5fb-658">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="cf5fb-659">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="cf5fb-660">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="cf5fb-661">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="cf5fb-662">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="cf5fb-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="cf5fb-663">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="cf5fb-664">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="cf5fb-665">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="cf5fb-666">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="cf5fb-667">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="cf5fb-668">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="cf5fb-669">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="cf5fb-670">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="cf5fb-671">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="cf5fb-672">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="cf5fb-673">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="cf5fb-674">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="cf5fb-675">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="cf5fb-676">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="cf5fb-676">Maximum streams per connection</span></span>

<span data-ttu-id="cf5fb-677">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="cf5fb-678">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="cf5fb-679">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="cf5fb-680">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-680">Header table size</span></span>

<span data-ttu-id="cf5fb-681">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="cf5fb-682">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="cf5fb-683">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="cf5fb-684">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="cf5fb-685">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-685">Maximum frame size</span></span>

<span data-ttu-id="cf5fb-686">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="cf5fb-687">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="cf5fb-688">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="cf5fb-689">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-689">Maximum request header size</span></span>

<span data-ttu-id="cf5fb-690">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="cf5fb-691">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="cf5fb-692">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="cf5fb-693">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="cf5fb-694">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-694">Initial connection window size</span></span>

<span data-ttu-id="cf5fb-695">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="cf5fb-696">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="cf5fb-697">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="cf5fb-698">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="cf5fb-699">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-699">Initial stream window size</span></span>

<span data-ttu-id="cf5fb-700">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="cf5fb-701">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="cf5fb-702">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="cf5fb-703">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="cf5fb-704">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="cf5fb-704">Synchronous I/O</span></span>

<span data-ttu-id="cf5fb-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="cf5fb-706">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-707">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="cf5fb-708">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="cf5fb-709">同期 I/O を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="cf5fb-710">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="cf5fb-711">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-711">Endpoint configuration</span></span>

<span data-ttu-id="cf5fb-712">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="cf5fb-713">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="cf5fb-714">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-714">Specify URLs using the:</span></span>

* <span data-ttu-id="cf5fb-715">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="cf5fb-716">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="cf5fb-717">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="cf5fb-718">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="cf5fb-719">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="cf5fb-720">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="cf5fb-721">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="cf5fb-722">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-722">A development certificate is created:</span></span>

* <span data-ttu-id="cf5fb-723">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="cf5fb-724">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="cf5fb-725">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="cf5fb-726">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cf5fb-727"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="cf5fb-728">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="cf5fb-729">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="cf5fb-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="cf5fb-731">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="cf5fb-732">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-733"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="cf5fb-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="cf5fb-735">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="cf5fb-736">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-737"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="cf5fb-738">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="cf5fb-739">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="cf5fb-740">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="cf5fb-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="cf5fb-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="cf5fb-742">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="cf5fb-743">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="cf5fb-744">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="cf5fb-745">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-745">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="cf5fb-746">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="cf5fb-747">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="cf5fb-748">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="cf5fb-749">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="cf5fb-750">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="cf5fb-751">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="cf5fb-752">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="cf5fb-753">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="cf5fb-754">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="cf5fb-755">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="cf5fb-756">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="cf5fb-757">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="cf5fb-758">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-758">Supported configurations described next:</span></span>

* <span data-ttu-id="cf5fb-759">構成なし</span><span class="sxs-lookup"><span data-stu-id="cf5fb-759">No configuration</span></span>
* <span data-ttu-id="cf5fb-760">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="cf5fb-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="cf5fb-761">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="cf5fb-761">Change the defaults in code</span></span>

<span data-ttu-id="cf5fb-762">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-762">*No configuration*</span></span>

<span data-ttu-id="cf5fb-763">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="cf5fb-764">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="cf5fb-765">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="cf5fb-766">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="cf5fb-767">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="cf5fb-768">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="cf5fb-769">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="cf5fb-770">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="cf5fb-771">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="cf5fb-772">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="cf5fb-773">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-773">Schema notes:</span></span>

* <span data-ttu-id="cf5fb-774">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="cf5fb-775">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="cf5fb-776">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="cf5fb-777">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="cf5fb-778">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="cf5fb-779">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="cf5fb-780">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="cf5fb-781">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="cf5fb-782">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="cf5fb-783">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="cf5fb-784">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="cf5fb-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="cf5fb-786">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="cf5fb-787">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="cf5fb-788">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="cf5fb-789">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="cf5fb-790">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="cf5fb-791">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="cf5fb-792">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="cf5fb-793">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-793">*Change the defaults in code*</span></span>

<span data-ttu-id="cf5fb-794">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="cf5fb-795">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="cf5fb-796">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="cf5fb-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="cf5fb-798">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="cf5fb-799">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="cf5fb-800">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="cf5fb-801">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="cf5fb-802">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-802">SNI support requires:</span></span>

* <span data-ttu-id="cf5fb-803">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="cf5fb-804">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="cf5fb-805">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="cf5fb-806">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="cf5fb-807">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="cf5fb-808">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="cf5fb-808">Connection logging</span></span>

<span data-ttu-id="cf5fb-809">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="cf5fb-810">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="cf5fb-811">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="cf5fb-812">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="cf5fb-813">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-813">Bind to a TCP socket</span></span>

<span data-ttu-id="cf5fb-814"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="cf5fb-815">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="cf5fb-816">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="cf5fb-817">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-817">Bind to a Unix socket</span></span>

<span data-ttu-id="cf5fb-818">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="cf5fb-819">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="cf5fb-820">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="cf5fb-821">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="cf5fb-822">ポート 0</span><span class="sxs-lookup"><span data-stu-id="cf5fb-822">Port 0</span></span>

<span data-ttu-id="cf5fb-823">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="cf5fb-824">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="cf5fb-825">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="cf5fb-826">制限事項</span><span class="sxs-lookup"><span data-stu-id="cf5fb-826">Limitations</span></span>

<span data-ttu-id="cf5fb-827">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="cf5fb-828">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="cf5fb-829">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="cf5fb-829">`urls` host configuration key</span></span>
* <span data-ttu-id="cf5fb-830">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="cf5fb-831">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="cf5fb-832">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="cf5fb-833">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="cf5fb-834">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="cf5fb-835">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-835">IIS endpoint configuration</span></span>

<span data-ttu-id="cf5fb-836">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="cf5fb-837">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="cf5fb-838">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="cf5fb-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="cf5fb-839">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="cf5fb-840">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="cf5fb-841">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="cf5fb-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="cf5fb-842">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="cf5fb-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="cf5fb-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-844">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-845">'Identity'</span></span>
- <span data-ttu-id="cf5fb-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-847">'Razor'</span></span>
- <span data-ttu-id="cf5fb-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-850">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-851">'Identity'</span></span>
- <span data-ttu-id="cf5fb-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-853">'Razor'</span></span>
- <span data-ttu-id="cf5fb-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-856">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-857">'Identity'</span></span>
- <span data-ttu-id="cf5fb-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-859">'Razor'</span></span>
- <span data-ttu-id="cf5fb-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-862">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-863">'Identity'</span></span>
- <span data-ttu-id="cf5fb-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-865">'Razor'</span></span>
- <span data-ttu-id="cf5fb-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-868">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-869">'Identity'</span></span>
- <span data-ttu-id="cf5fb-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-871">'Razor'</span></span>
- <span data-ttu-id="cf5fb-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-874">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-875">'Identity'</span></span>
- <span data-ttu-id="cf5fb-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-877">'Razor'</span></span>
- <span data-ttu-id="cf5fb-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-880">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-881">'Identity'</span></span>
- <span data-ttu-id="cf5fb-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-883">'Razor'</span></span>
- <span data-ttu-id="cf5fb-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-886">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-887">'Identity'</span></span>
- <span data-ttu-id="cf5fb-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-889">'Razor'</span></span>
- <span data-ttu-id="cf5fb-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-892">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-893">'Identity'</span></span>
- <span data-ttu-id="cf5fb-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-895">'Razor'</span></span>
- <span data-ttu-id="cf5fb-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-898">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-899">'Identity'</span></span>
- <span data-ttu-id="cf5fb-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-901">'Razor'</span></span>
- <span data-ttu-id="cf5fb-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-904">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-905">'Identity'</span></span>
- <span data-ttu-id="cf5fb-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-907">'Razor'</span></span>
- <span data-ttu-id="cf5fb-908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-908">'SignalR' uid:</span></span> 

<span data-ttu-id="cf5fb-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-910">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-911">'Identity'</span></span>
- <span data-ttu-id="cf5fb-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-913">'Razor'</span></span>
- <span data-ttu-id="cf5fb-914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-916">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-917">'Identity'</span></span>
- <span data-ttu-id="cf5fb-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-919">'Razor'</span></span>
- <span data-ttu-id="cf5fb-920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-922">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-923">'Identity'</span></span>
- <span data-ttu-id="cf5fb-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-925">'Razor'</span></span>
- <span data-ttu-id="cf5fb-926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-928">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-929">'Identity'</span></span>
- <span data-ttu-id="cf5fb-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-931">'Razor'</span></span>
- <span data-ttu-id="cf5fb-932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-934">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-935">'Identity'</span></span>
- <span data-ttu-id="cf5fb-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-937">'Razor'</span></span>
- <span data-ttu-id="cf5fb-938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-940">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-941">'Identity'</span></span>
- <span data-ttu-id="cf5fb-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-943">'Razor'</span></span>
- <span data-ttu-id="cf5fb-944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-946">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-947">'Identity'</span></span>
- <span data-ttu-id="cf5fb-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-949">'Razor'</span></span>
- <span data-ttu-id="cf5fb-950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-952">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-953">'Identity'</span></span>
- <span data-ttu-id="cf5fb-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-955">'Razor'</span></span>
- <span data-ttu-id="cf5fb-956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-958">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-959">'Identity'</span></span>
- <span data-ttu-id="cf5fb-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-961">'Razor'</span></span>
- <span data-ttu-id="cf5fb-962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-964">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-965">'Identity'</span></span>
- <span data-ttu-id="cf5fb-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-967">'Razor'</span></span>
- <span data-ttu-id="cf5fb-968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-970">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-971">'Identity'</span></span>
- <span data-ttu-id="cf5fb-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-973">'Razor'</span></span>
- <span data-ttu-id="cf5fb-974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cf5fb-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cf5fb-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-976">'Blazor'</span></span>
- <span data-ttu-id="cf5fb-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-977">'Identity'</span></span>
- <span data-ttu-id="cf5fb-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="cf5fb-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cf5fb-979">'Razor'</span></span>
- <span data-ttu-id="cf5fb-980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-980">'SignalR' uid:</span></span> 

<span data-ttu-id="cf5fb-981">--------------- | | `Http1`                    | HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="cf5fb-982">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-982">Can be used with or without TLS.</span></span> <span data-ttu-id="cf5fb-983">| | `Http2`                    | HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="cf5fb-984">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="cf5fb-985">| | `Http1AndHttp2`            | HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="cf5fb-986">HTTP/2 には、TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="cf5fb-987">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="cf5fb-988">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="cf5fb-989">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="cf5fb-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="cf5fb-990">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="cf5fb-990">Renegotiation disabled</span></span>
* <span data-ttu-id="cf5fb-991">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="cf5fb-991">Compression disabled</span></span>
* <span data-ttu-id="cf5fb-992">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="cf5fb-993">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;:224 ビット以上</span><span class="sxs-lookup"><span data-stu-id="cf5fb-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="cf5fb-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;:2048 ビット以上</span><span class="sxs-lookup"><span data-stu-id="cf5fb-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="cf5fb-995">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="cf5fb-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="cf5fb-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="cf5fb-997">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="cf5fb-998">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-998">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="cf5fb-999">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="cf5fb-1000">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="cf5fb-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="cf5fb-1002">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="cf5fb-1003">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="cf5fb-1004">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="cf5fb-1005">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1005">Transport configuration</span></span>

<span data-ttu-id="cf5fb-1006">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="cf5fb-1007">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="cf5fb-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="cf5fb-1009">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="cf5fb-1010">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="cf5fb-1011">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="cf5fb-1012"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="cf5fb-1013">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1013">URL prefixes</span></span>

<span data-ttu-id="cf5fb-1014">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="cf5fb-1015">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="cf5fb-1016">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="cf5fb-1017">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="cf5fb-1018">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="cf5fb-1019">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="cf5fb-1020">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="cf5fb-1021">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="cf5fb-1022">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="cf5fb-1023">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="cf5fb-1024">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="cf5fb-1025">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="cf5fb-1026">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="cf5fb-1027">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="cf5fb-1028">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="cf5fb-1029">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="cf5fb-1030">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1030">Host filtering</span></span>

<span data-ttu-id="cf5fb-1031">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="cf5fb-1032">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="cf5fb-1033">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="cf5fb-1034">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="cf5fb-1035">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="cf5fb-1036">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="cf5fb-1037">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="cf5fb-1038">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="cf5fb-1039">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<EnvironmentName>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="cf5fb-1040">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="cf5fb-1041">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="cf5fb-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="cf5fb-1043">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="cf5fb-1044">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="cf5fb-1045">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="cf5fb-1046">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="cf5fb-1047">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="cf5fb-1048">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="cf5fb-1049">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="cf5fb-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1050">HTTPS</span></span>
* <span data-ttu-id="cf5fb-1051">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="cf5fb-1052">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="cf5fb-1053">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="cf5fb-1054">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="cf5fb-1055">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="cf5fb-1056">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="cf5fb-1057">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="cf5fb-1058">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="cf5fb-1060">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1060">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="cf5fb-1062">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="cf5fb-1063">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="cf5fb-1064">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="cf5fb-1065">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="cf5fb-1066">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="cf5fb-1067">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1067">A reverse proxy:</span></span>

* <span data-ttu-id="cf5fb-1068">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="cf5fb-1069">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="cf5fb-1070">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="cf5fb-1071">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="cf5fb-1072">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-1073">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="cf5fb-1074">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="cf5fb-1075">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cf5fb-1076">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="cf5fb-1077">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="cf5fb-1078">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="cf5fb-1079">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="cf5fb-1080">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1080">Kestrel options</span></span>

<span data-ttu-id="cf5fb-1081">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="cf5fb-1082"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="cf5fb-1083">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="cf5fb-1084"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="cf5fb-1085">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cf5fb-1086">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="cf5fb-1087">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="cf5fb-1088">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="cf5fb-1089">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="cf5fb-1090">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="cf5fb-1091">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="cf5fb-1092">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="cf5fb-1093">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="cf5fb-1094">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="cf5fb-1095">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="cf5fb-1096">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="cf5fb-1097">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="cf5fb-1098">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="cf5fb-1099">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="cf5fb-1100">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="cf5fb-1101">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="cf5fb-1102">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="cf5fb-1103">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="cf5fb-1104">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="cf5fb-1105">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="cf5fb-1106">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="cf5fb-1107">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="cf5fb-1108">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="cf5fb-1109">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="cf5fb-1110">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="cf5fb-1111">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="cf5fb-1112">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="cf5fb-1113">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="cf5fb-1114">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="cf5fb-1115">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="cf5fb-1116">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="cf5fb-1117">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="cf5fb-1118">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="cf5fb-1119">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="cf5fb-1120">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="cf5fb-1121">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="cf5fb-1122">同期 I/O</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1122">Synchronous I/O</span></span>

<span data-ttu-id="cf5fb-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 I/O を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="cf5fb-1124">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="cf5fb-1125">ブロッキング同期 I/O 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="cf5fb-1126">非同期 I/O をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="cf5fb-1127">同期 I/O を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="cf5fb-1128">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="cf5fb-1129">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1129">Endpoint configuration</span></span>

<span data-ttu-id="cf5fb-1130">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="cf5fb-1131">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="cf5fb-1132">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="cf5fb-1133">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="cf5fb-1134">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="cf5fb-1135">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="cf5fb-1136">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="cf5fb-1137">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="cf5fb-1138">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="cf5fb-1139">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="cf5fb-1140">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1140">A development certificate is created:</span></span>

* <span data-ttu-id="cf5fb-1141">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="cf5fb-1142">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="cf5fb-1143">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="cf5fb-1144">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cf5fb-1145"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="cf5fb-1146">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="cf5fb-1147">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="cf5fb-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="cf5fb-1149">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="cf5fb-1150">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-1151"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="cf5fb-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="cf5fb-1153">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="cf5fb-1154">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="cf5fb-1155"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="cf5fb-1156">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="cf5fb-1157">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="cf5fb-1158">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="cf5fb-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="cf5fb-1160">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="cf5fb-1161">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="cf5fb-1162">`UseHttps`:既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="cf5fb-1163">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1163">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="cf5fb-1164">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="cf5fb-1165">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="cf5fb-1166">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="cf5fb-1167">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="cf5fb-1168">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="cf5fb-1169">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="cf5fb-1170">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="cf5fb-1171">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="cf5fb-1172">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="cf5fb-1173">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="cf5fb-1174">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="cf5fb-1175">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="cf5fb-1176">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="cf5fb-1177">構成なし</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1177">No configuration</span></span>
* <span data-ttu-id="cf5fb-1178">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="cf5fb-1179">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1179">Change the defaults in code</span></span>

<span data-ttu-id="cf5fb-1180">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1180">*No configuration*</span></span>

<span data-ttu-id="cf5fb-1181">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="cf5fb-1182">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="cf5fb-1183">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="cf5fb-1184">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="cf5fb-1185">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="cf5fb-1186">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="cf5fb-1187">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="cf5fb-1188">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="cf5fb-1189">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="cf5fb-1190">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="cf5fb-1191">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1191">Schema notes:</span></span>

* <span data-ttu-id="cf5fb-1192">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="cf5fb-1193">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="cf5fb-1194">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="cf5fb-1195">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="cf5fb-1196">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="cf5fb-1197">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="cf5fb-1198">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="cf5fb-1199">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="cf5fb-1200">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="cf5fb-1201">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="cf5fb-1202">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="cf5fb-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="cf5fb-1204">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="cf5fb-1205">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="cf5fb-1206">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="cf5fb-1207">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="cf5fb-1208">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="cf5fb-1209">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="cf5fb-1210">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="cf5fb-1211">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="cf5fb-1212">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="cf5fb-1213">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="cf5fb-1214">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="cf5fb-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="cf5fb-1216">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="cf5fb-1217">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="cf5fb-1218">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="cf5fb-1219">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="cf5fb-1220">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1220">SNI support requires:</span></span>

* <span data-ttu-id="cf5fb-1221">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="cf5fb-1222">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="cf5fb-1223">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="cf5fb-1224">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="cf5fb-1225">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="cf5fb-1226">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1226">Connection logging</span></span>

<span data-ttu-id="cf5fb-1227">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="cf5fb-1228">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="cf5fb-1229">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="cf5fb-1230">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="cf5fb-1231">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="cf5fb-1232"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="cf5fb-1233">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="cf5fb-1234">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="cf5fb-1235">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="cf5fb-1236">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="cf5fb-1237">Nginx 構成ファイルで `server` > `location` > `proxy_pass` エントリを `http://unix:/tmp/{KESTREL SOCKET}:/;` に設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="cf5fb-1238">`{KESTREL SOCKET}` は <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> に提供されたソケットの名前です (たとえば、前の例の `kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="cf5fb-1239">ソケットが Nginx によって書き込み可能であることを確認します (たとえば、`chmod go+w /tmp/kestrel-test.sock`)。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="cf5fb-1240">ポート 0</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1240">Port 0</span></span>

<span data-ttu-id="cf5fb-1241">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="cf5fb-1242">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="cf5fb-1243">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="cf5fb-1244">制限事項</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1244">Limitations</span></span>

<span data-ttu-id="cf5fb-1245">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="cf5fb-1246">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="cf5fb-1247">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="cf5fb-1248">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="cf5fb-1249">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="cf5fb-1250">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="cf5fb-1251">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="cf5fb-1252">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="cf5fb-1253">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="cf5fb-1254">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="cf5fb-1255">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="cf5fb-1256">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1256">Transport configuration</span></span>

<span data-ttu-id="cf5fb-1257">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="cf5fb-1258">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="cf5fb-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="cf5fb-1260">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="cf5fb-1261">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="cf5fb-1262">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="cf5fb-1263"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="cf5fb-1264">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1264">URL prefixes</span></span>

<span data-ttu-id="cf5fb-1265">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="cf5fb-1266">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="cf5fb-1267">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="cf5fb-1268">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="cf5fb-1269">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="cf5fb-1270">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="cf5fb-1271">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="cf5fb-1272">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="cf5fb-1273">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="cf5fb-1274">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="cf5fb-1275">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="cf5fb-1276">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="cf5fb-1277">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="cf5fb-1278">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="cf5fb-1279">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="cf5fb-1280">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="cf5fb-1281">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1281">Host filtering</span></span>

<span data-ttu-id="cf5fb-1282">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="cf5fb-1283">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="cf5fb-1284">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="cf5fb-1285">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="cf5fb-1286">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="cf5fb-1287">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="cf5fb-1288">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="cf5fb-1289">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="cf5fb-1290">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<EnvironmentName>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="cf5fb-1291">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="cf5fb-1292">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="cf5fb-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="cf5fb-1294">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="cf5fb-1295">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="cf5fb-1296">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="cf5fb-1297">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="cf5fb-1298">HTTP/1.1 要求のドレイン</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="cf5fb-1299">HTTP 接続を開くには時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="cf5fb-1300">HTTPS の場合も、リソースが大量に消費されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="cf5fb-1301">そのため、Kestrel は、HTTP/1.1 プロトコルごとに接続を再利用しようとします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="cf5fb-1302">接続を再利用できるようにするには、要求本文を完全に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="cf5fb-1303">アプリは、サーバーがリダイレクトまたは 404 応答を返す `POST` 要求のように、常に要求本文を使用するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="cf5fb-1304">`POST`-リダイレクトの場合:</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="cf5fb-1305">クライアントが `POST` データの一部を既に送信している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="cf5fb-1306">サーバーは 301 応答を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="cf5fb-1307">以前の要求本文の `POST` データが完全に読み取られるまで、新しい要求に対して接続を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="cf5fb-1308">Kestrel は、要求本文のドレインを試行します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="cf5fb-1309">要求本文のドレインは、データを処理せずに読み取りおよび破棄を行うことを意味します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="cf5fb-1310">ドレイン プロセスでは、接続が再利用できるようになる代わりに、残りのデータをドレインする時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="cf5fb-1311">ドレインのタイムアウトは 5 秒であり、この設定は構成できません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="cf5fb-1312">`Content-Length` または `Transfer-Encoding` ヘッダーで指定されたすべてのデータがタイムアウト前に読み取られていない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="cf5fb-1313">場合によっては、応答の書き込みの前後に要求をすぐに終了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="cf5fb-1314">たとえば、クライアントのデータ キャップが制限されているため、アップロードするデータの制限が優先される場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="cf5fb-1315">このような場合、要求を終了するには、コントローラー、Razor ページ、またはミドルウェアから [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="cf5fb-1316">`Abort` を呼び出す際には、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="cf5fb-1317">新しい接続の作成には、時間とコストがかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="cf5fb-1318">接続が閉じる前にクライアントが応答を読み取ることは保証されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="cf5fb-1319">`Abort` の呼び出しは最小限に抑え、一般的なエラーではなく重大なエラーが発生した場合のために予約する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="cf5fb-1320">`Abort` は特定の問題を解決する必要がある場合にのみ、呼び出してください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="cf5fb-1321">たとえば、悪意のあるクライアントがデータを `POST` しようとしている場合や、クライアント コードに大量または多数の要求を引き起こすバグがある場合に `Abort` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="cf5fb-1322">HTTP 404 (Not Found) などの一般的なエラー状況では、`Abort` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="cf5fb-1323">`Abort` を呼び出す前に [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) を呼び出すと、サーバーが応答の書き込みを完了したことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="cf5fb-1324">ただし、クライアントの動作は予測できないため、接続が中止される前に応答が読み取られない場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="cf5fb-1325">プロトコルでは、接続を閉じずに個々の要求ストリームを中止することがサポートされているため、HTTP/2 では、このプロセスが異なります。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="cf5fb-1326">5 秒のドレイン タイムアウトは適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="cf5fb-1327">応答の完了後に未読の要求本文データがある場合、サーバーは HTTP/2 RST フレームを送信します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="cf5fb-1328">追加の要求本文データ フレームは無視されます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="cf5fb-1329">可能なかぎり、クライアントが [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) の要求ヘッダーを利用し、要求本文の送信を開始する前にサーバーの応答を待機することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="cf5fb-1330">これにより、クライアントは、不要なデータを送信する前に、応答を調べて中止することができます。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf5fb-1331">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1331">Additional resources</span></span>

* <span data-ttu-id="cf5fb-1332">Linux で UNIX ソケットを使用している場合、アプリのシャットダウン時にソケットは自動的に削除されません。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="cf5fb-1333">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/aspnetcore/issues/14134)します。</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="cf5fb-1334">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="cf5fb-1334">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
