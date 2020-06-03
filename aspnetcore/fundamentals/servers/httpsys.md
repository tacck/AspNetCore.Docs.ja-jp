---
<span data-ttu-id="aaaa9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-102">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-103">'Identity'</span></span>
- <span data-ttu-id="aaaa9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-105">'Razor'</span></span>
- <span data-ttu-id="aaaa9-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-106">'SignalR' uid:</span></span> 

---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="aaaa9-107">ASP.NET Core での HTTP.sys Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="aaaa9-107">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="aaaa9-108">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-108">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="aaaa9-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="aaaa9-110">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-110">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aaaa9-111">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-111">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-112">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-112">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="aaaa9-113">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-113">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="aaaa9-114">ポート共有</span><span class="sxs-lookup"><span data-stu-id="aaaa9-114">Port sharing</span></span>
* <span data-ttu-id="aaaa9-115">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="aaaa9-115">HTTPS with SNI</span></span>
* <span data-ttu-id="aaaa9-116">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-116">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="aaaa9-117">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="aaaa9-117">Direct file transmission</span></span>
* <span data-ttu-id="aaaa9-118">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-118">Response caching</span></span>
* <span data-ttu-id="aaaa9-119">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-119">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="aaaa9-120">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-120">Supported Windows versions:</span></span>

* <span data-ttu-id="aaaa9-121">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-121">Windows 7 or later</span></span>
* <span data-ttu-id="aaaa9-122">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-122">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aaaa9-123">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="aaaa9-124">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="aaaa9-124">When to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-125">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-125">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="aaaa9-126">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-126">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="aaaa9-128">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-128">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="aaaa9-130">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-130">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="aaaa9-131">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-131">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="aaaa9-132">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="aaaa9-132">HTTP/2 support</span></span>

<span data-ttu-id="aaaa9-133">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-133">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aaaa9-134">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-134">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="aaaa9-135">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-135">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aaaa9-136">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-136">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aaaa9-137">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-137">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="aaaa9-138">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-138">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="aaaa9-139">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-139">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="aaaa9-140">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-140">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="aaaa9-141">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-141">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="aaaa9-142">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-142">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="aaaa9-143">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-143">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="aaaa9-144">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-144">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="aaaa9-145">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-145">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="aaaa9-146">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="aaaa9-146">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="aaaa9-147">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-147">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-148">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="aaaa9-149">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-149">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="aaaa9-150">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-150">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="aaaa9-151">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-151">**HTTP.sys options**</span></span>

| <span data-ttu-id="aaaa9-152">プロパティ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-152">Property</span></span> | <span data-ttu-id="aaaa9-153">説明</span><span class="sxs-lookup"><span data-stu-id="aaaa9-153">Description</span></span> | <span data-ttu-id="aaaa9-154">Default</span><span class="sxs-lookup"><span data-stu-id="aaaa9-154">Default</span></span> |
| ---
<span data-ttu-id="aaaa9-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-156">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-157">'Identity'</span></span>
- <span data-ttu-id="aaaa9-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-159">'Razor'</span></span>
- <span data-ttu-id="aaaa9-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-162">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-163">'Identity'</span></span>
- <span data-ttu-id="aaaa9-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-165">'Razor'</span></span>
- <span data-ttu-id="aaaa9-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-166">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-168">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-169">'Identity'</span></span>
- <span data-ttu-id="aaaa9-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-171">'Razor'</span></span>
- <span data-ttu-id="aaaa9-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-174">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-175">'Identity'</span></span>
- <span data-ttu-id="aaaa9-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-177">'Razor'</span></span>
- <span data-ttu-id="aaaa9-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-180">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-181">'Identity'</span></span>
- <span data-ttu-id="aaaa9-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-183">'Razor'</span></span>
- <span data-ttu-id="aaaa9-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-184">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="aaaa9-186"> | `false` | | [AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-186"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="aaaa9-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="aaaa9-188">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-188">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-189">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-189">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="aaaa9-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="aaaa9-191">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-191">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="aaaa9-192">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-192">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="aaaa9-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 同時受け入れの最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="aaaa9-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 受け入れるコンカレント接続の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="aaaa9-195">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-195">Use `-1` for infinite.</span></span> <span data-ttu-id="aaaa9-196">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-196">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="aaaa9-197">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="aaaa9-197">(machine-wide</span></span><br><span data-ttu-id="aaaa9-198">設定) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-198">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="aaaa9-199">| 30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-199">| 30000000 bytes</span></span><br><span data-ttu-id="aaaa9-200">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-200">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="aaaa9-201">| 1000 | | `RequestQueueMode` | これは、サーバーで要求キューの作成と構成を行う必要があるかどうか、または既存のキューにアタッチする必要があるかを示します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-201">| 1000 | | `RequestQueueMode` | This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="aaaa9-202">既存のキューにアタッチする場合、既存の構成オプションのほとんどは適用されません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-202">Most existing configuration options do not apply when attaching to an existing queue.</span></span><span data-ttu-id="aaaa9-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | HTTP.sys 要求キューの名前。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | The name of the HTTP.sys request queue.</span></span><span data-ttu-id="aaaa9-204"> | `null` (匿名キュー) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-204"> | `null` (Anonymous queue) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="aaaa9-205">(正常に完了) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-205">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="aaaa9-206">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-206">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="aaaa9-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="aaaa9-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="aaaa9-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="aaaa9-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="aaaa9-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="aaaa9-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="aaaa9-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="aaaa9-214">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-214">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="aaaa9-215">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-215">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-216">|  |</span><span class="sxs-lookup"><span data-stu-id="aaaa9-216">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="aaaa9-217">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-217">**MaxRequestBodySize**</span></span>

<span data-ttu-id="aaaa9-218">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-218">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="aaaa9-219">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-219">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="aaaa9-220">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-220">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="aaaa9-221">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-221">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aaaa9-222">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-222">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="aaaa9-223">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-223">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aaaa9-224">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-224">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="aaaa9-225">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-225">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-226">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-226">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="aaaa9-227">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-227">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="aaaa9-229">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-229">Configure Windows Server</span></span>

1. <span data-ttu-id="aaaa9-230">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-230">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="aaaa9-231">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-231">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-232">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-232">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="aaaa9-233">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-233">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-234">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-234">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="aaaa9-235">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-235">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="aaaa9-236">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-236">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="aaaa9-237">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-237">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="aaaa9-238">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-238">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="aaaa9-239">**.NET Core**: アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-239">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="aaaa9-240">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-240">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="aaaa9-241">**.NET Framework**:アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-241">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="aaaa9-242">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-242">Install the required .NET Framework.</span></span> <span data-ttu-id="aaaa9-243">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-243">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="aaaa9-244">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-244">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="aaaa9-245">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-245">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="aaaa9-246">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-246">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="aaaa9-247">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-247">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="aaaa9-248">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-248">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="aaaa9-249">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-249">`urls` command-line argument</span></span>
   * <span data-ttu-id="aaaa9-250">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-250">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="aaaa9-251">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-251">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="aaaa9-252">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-252">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="aaaa9-253">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-253">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="aaaa9-254">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-254">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="aaaa9-255">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-255">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="aaaa9-256">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-256">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="aaaa9-257">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-257">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="aaaa9-258">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-258">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="aaaa9-259">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-259">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="aaaa9-260">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-260">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="aaaa9-261">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-261">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="aaaa9-262">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-262">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="aaaa9-263">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-263">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="aaaa9-264">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-264">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="aaaa9-265">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-265">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-266">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-266">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="aaaa9-267">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-267">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="aaaa9-268">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-268">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-269">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-269">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="aaaa9-270">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="aaaa9-270">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="aaaa9-271">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-271">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="aaaa9-272">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-272">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="aaaa9-273">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-273">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="aaaa9-274">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-274">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="aaaa9-275">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-275">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="aaaa9-276">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-276">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="aaaa9-277">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-277">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="aaaa9-278">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-278">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-279">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-279">Use a valid IP address.</span></span>
   * <span data-ttu-id="aaaa9-280">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-280">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="aaaa9-281">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-281">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="aaaa9-282">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-282">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="aaaa9-283">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-283">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="aaaa9-284">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-284">In Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-285">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-285">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="aaaa9-286">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-286">Select the **Package** tab.</span></span>
     * <span data-ttu-id="aaaa9-287">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-287">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="aaaa9-288">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-288">When not using Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-289">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-289">Open the app's project file.</span></span>
     * <span data-ttu-id="aaaa9-290">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-290">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="aaaa9-291">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-291">In the following example:</span></span>

   * <span data-ttu-id="aaaa9-292">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-292">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="aaaa9-293">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-293">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="aaaa9-294">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-294">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="aaaa9-295">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-295">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="aaaa9-296">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-296">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="aaaa9-297">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-297">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="aaaa9-298">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-298">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="aaaa9-299">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-299">Run the app.</span></span>

   <span data-ttu-id="aaaa9-300">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-300">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="aaaa9-301">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-301">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-302">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-302">The app responds at the server's public IP address.</span></span> <span data-ttu-id="aaaa9-303">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-303">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="aaaa9-304">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-304">A development certificate is used in this example.</span></span> <span data-ttu-id="aaaa9-305">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-305">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="aaaa9-307">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-307">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="aaaa9-308">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-308">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="aaaa9-309">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-309">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaaa9-310">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aaaa9-310">Additional resources</span></span>

* [<span data-ttu-id="aaaa9-311">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="aaaa9-311">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="aaaa9-312">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="aaaa9-312">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="aaaa9-313">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-313">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="aaaa9-314">ホスト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-314">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="aaaa9-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="aaaa9-316">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-316">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aaaa9-317">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-317">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-318">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-318">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="aaaa9-319">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-319">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="aaaa9-320">ポート共有</span><span class="sxs-lookup"><span data-stu-id="aaaa9-320">Port sharing</span></span>
* <span data-ttu-id="aaaa9-321">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="aaaa9-321">HTTPS with SNI</span></span>
* <span data-ttu-id="aaaa9-322">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-322">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="aaaa9-323">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="aaaa9-323">Direct file transmission</span></span>
* <span data-ttu-id="aaaa9-324">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-324">Response caching</span></span>
* <span data-ttu-id="aaaa9-325">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-325">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="aaaa9-326">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-326">Supported Windows versions:</span></span>

* <span data-ttu-id="aaaa9-327">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-327">Windows 7 or later</span></span>
* <span data-ttu-id="aaaa9-328">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-328">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aaaa9-329">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-329">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="aaaa9-330">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="aaaa9-330">When to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-331">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-331">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="aaaa9-332">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-332">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="aaaa9-334">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-334">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="aaaa9-336">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-336">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="aaaa9-337">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-337">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="aaaa9-338">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="aaaa9-338">HTTP/2 support</span></span>

<span data-ttu-id="aaaa9-339">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-339">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aaaa9-340">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-340">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="aaaa9-341">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-341">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aaaa9-342">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-342">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aaaa9-343">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-343">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="aaaa9-344">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-344">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="aaaa9-345">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-345">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="aaaa9-346">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-346">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="aaaa9-347">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-347">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="aaaa9-348">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-348">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="aaaa9-349">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-349">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="aaaa9-350">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-350">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="aaaa9-351">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-351">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="aaaa9-352">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="aaaa9-352">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="aaaa9-353">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-353">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-354">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-354">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="aaaa9-355">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-355">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="aaaa9-356">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-356">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="aaaa9-357">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-357">**HTTP.sys options**</span></span>

| <span data-ttu-id="aaaa9-358">プロパティ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-358">Property</span></span> | <span data-ttu-id="aaaa9-359">説明</span><span class="sxs-lookup"><span data-stu-id="aaaa9-359">Description</span></span> | <span data-ttu-id="aaaa9-360">Default</span><span class="sxs-lookup"><span data-stu-id="aaaa9-360">Default</span></span> |
| ---
<span data-ttu-id="aaaa9-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-362">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-362">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-363">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-363">'Identity'</span></span>
- <span data-ttu-id="aaaa9-364">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-364">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-365">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-365">'Razor'</span></span>
- <span data-ttu-id="aaaa9-366">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-366">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-368">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-369">'Identity'</span></span>
- <span data-ttu-id="aaaa9-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-371">'Razor'</span></span>
- <span data-ttu-id="aaaa9-372">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-372">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-374">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-374">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-375">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-375">'Identity'</span></span>
- <span data-ttu-id="aaaa9-376">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-376">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-377">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-377">'Razor'</span></span>
- <span data-ttu-id="aaaa9-378">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-378">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-380">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-381">'Identity'</span></span>
- <span data-ttu-id="aaaa9-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-383">'Razor'</span></span>
- <span data-ttu-id="aaaa9-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-386">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-386">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-387">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-387">'Identity'</span></span>
- <span data-ttu-id="aaaa9-388">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-388">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-389">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-389">'Razor'</span></span>
- <span data-ttu-id="aaaa9-390">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-390">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="aaaa9-392"> | `false` | | [AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-392"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="aaaa9-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="aaaa9-394">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-394">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-395">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-395">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="aaaa9-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="aaaa9-397">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-397">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="aaaa9-398">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-398">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="aaaa9-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 同時受け入れの最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="aaaa9-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 受け入れるコンカレント接続の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="aaaa9-401">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-401">Use `-1` for infinite.</span></span> <span data-ttu-id="aaaa9-402">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-402">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="aaaa9-403">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="aaaa9-403">(machine-wide</span></span><br><span data-ttu-id="aaaa9-404">設定) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-404">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="aaaa9-405">| 30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-405">| 30000000 bytes</span></span><br><span data-ttu-id="aaaa9-406">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-406">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="aaaa9-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="aaaa9-408">(正常に完了) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-408">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="aaaa9-409">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-409">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="aaaa9-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="aaaa9-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="aaaa9-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="aaaa9-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="aaaa9-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="aaaa9-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="aaaa9-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="aaaa9-417">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-417">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="aaaa9-418">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-418">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-419">|  |</span><span class="sxs-lookup"><span data-stu-id="aaaa9-419">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="aaaa9-420">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-420">**MaxRequestBodySize**</span></span>

<span data-ttu-id="aaaa9-421">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-421">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="aaaa9-422">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-422">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="aaaa9-423">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-423">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="aaaa9-424">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-424">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aaaa9-425">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-425">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="aaaa9-426">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-426">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aaaa9-427">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-427">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="aaaa9-428">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-428">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-429">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-429">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="aaaa9-430">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-430">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="aaaa9-432">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-432">Configure Windows Server</span></span>

1. <span data-ttu-id="aaaa9-433">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-433">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="aaaa9-434">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-434">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-435">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-435">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="aaaa9-436">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-436">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-437">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-437">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="aaaa9-438">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-438">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="aaaa9-439">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-439">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="aaaa9-440">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-440">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="aaaa9-441">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-441">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="aaaa9-442">**.NET Core**: アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-442">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="aaaa9-443">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-443">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="aaaa9-444">**.NET Framework**:アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-444">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="aaaa9-445">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-445">Install the required .NET Framework.</span></span> <span data-ttu-id="aaaa9-446">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-446">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="aaaa9-447">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-447">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="aaaa9-448">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-448">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="aaaa9-449">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-449">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="aaaa9-450">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-450">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="aaaa9-451">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-451">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="aaaa9-452">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-452">`urls` command-line argument</span></span>
   * <span data-ttu-id="aaaa9-453">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-453">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="aaaa9-454">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-454">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="aaaa9-455">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-455">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="aaaa9-456">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-456">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="aaaa9-457">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-457">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="aaaa9-458">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-458">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="aaaa9-459">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-459">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="aaaa9-460">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-460">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="aaaa9-461">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-461">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="aaaa9-462">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-462">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="aaaa9-463">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-463">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="aaaa9-464">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-464">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="aaaa9-465">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-465">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="aaaa9-466">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-466">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="aaaa9-467">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-467">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="aaaa9-468">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-468">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-469">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-469">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="aaaa9-470">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-470">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="aaaa9-471">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-471">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-472">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-472">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="aaaa9-473">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="aaaa9-473">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="aaaa9-474">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-474">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="aaaa9-475">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-475">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="aaaa9-476">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-476">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="aaaa9-477">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-477">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="aaaa9-478">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-478">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="aaaa9-479">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-479">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="aaaa9-480">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-480">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="aaaa9-481">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-481">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-482">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-482">Use a valid IP address.</span></span>
   * <span data-ttu-id="aaaa9-483">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-483">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="aaaa9-484">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-484">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="aaaa9-485">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-485">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="aaaa9-486">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-486">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="aaaa9-487">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-487">In Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-488">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-488">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="aaaa9-489">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-489">Select the **Package** tab.</span></span>
     * <span data-ttu-id="aaaa9-490">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-490">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="aaaa9-491">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-491">When not using Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-492">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-492">Open the app's project file.</span></span>
     * <span data-ttu-id="aaaa9-493">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-493">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="aaaa9-494">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-494">In the following example:</span></span>

   * <span data-ttu-id="aaaa9-495">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-495">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="aaaa9-496">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-496">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="aaaa9-497">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-497">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="aaaa9-498">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-498">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="aaaa9-499">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-499">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="aaaa9-500">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-500">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="aaaa9-501">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-501">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="aaaa9-502">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-502">Run the app.</span></span>

   <span data-ttu-id="aaaa9-503">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-503">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="aaaa9-504">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-504">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-505">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-505">The app responds at the server's public IP address.</span></span> <span data-ttu-id="aaaa9-506">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-506">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="aaaa9-507">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-507">A development certificate is used in this example.</span></span> <span data-ttu-id="aaaa9-508">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-508">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="aaaa9-510">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-510">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="aaaa9-511">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-511">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="aaaa9-512">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-512">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaaa9-513">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aaaa9-513">Additional resources</span></span>

* [<span data-ttu-id="aaaa9-514">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="aaaa9-514">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="aaaa9-515">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="aaaa9-515">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="aaaa9-516">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-516">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="aaaa9-517">ホスト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-517">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="aaaa9-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="aaaa9-519">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-519">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aaaa9-520">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-520">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-521">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-521">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="aaaa9-522">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-522">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="aaaa9-523">ポート共有</span><span class="sxs-lookup"><span data-stu-id="aaaa9-523">Port sharing</span></span>
* <span data-ttu-id="aaaa9-524">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="aaaa9-524">HTTPS with SNI</span></span>
* <span data-ttu-id="aaaa9-525">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-525">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="aaaa9-526">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="aaaa9-526">Direct file transmission</span></span>
* <span data-ttu-id="aaaa9-527">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-527">Response caching</span></span>
* <span data-ttu-id="aaaa9-528">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-528">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="aaaa9-529">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-529">Supported Windows versions:</span></span>

* <span data-ttu-id="aaaa9-530">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-530">Windows 7 or later</span></span>
* <span data-ttu-id="aaaa9-531">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-531">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aaaa9-532">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="aaaa9-533">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="aaaa9-533">When to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-534">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-534">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="aaaa9-535">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-535">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="aaaa9-537">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-537">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="aaaa9-539">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-539">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="aaaa9-540">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-540">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="aaaa9-541">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="aaaa9-541">HTTP/2 support</span></span>

<span data-ttu-id="aaaa9-542">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-542">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aaaa9-543">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-543">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="aaaa9-544">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-544">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aaaa9-545">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-545">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aaaa9-546">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-546">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="aaaa9-547">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-547">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="aaaa9-548">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-548">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="aaaa9-549">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-549">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="aaaa9-550">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-550">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="aaaa9-551">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-551">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="aaaa9-552">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-552">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="aaaa9-553">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-553">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="aaaa9-554">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-554">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="aaaa9-555">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="aaaa9-555">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="aaaa9-556">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-556">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-557">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-557">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="aaaa9-558">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-558">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="aaaa9-559">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-559">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="aaaa9-560">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-560">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="aaaa9-561">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-561">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="aaaa9-562">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-562">**HTTP.sys options**</span></span>

| <span data-ttu-id="aaaa9-563">プロパティ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-563">Property</span></span> | <span data-ttu-id="aaaa9-564">説明</span><span class="sxs-lookup"><span data-stu-id="aaaa9-564">Description</span></span> | <span data-ttu-id="aaaa9-565">Default</span><span class="sxs-lookup"><span data-stu-id="aaaa9-565">Default</span></span> |
| ---
<span data-ttu-id="aaaa9-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-567">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-568">'Identity'</span></span>
- <span data-ttu-id="aaaa9-569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-569">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-570">'Razor'</span></span>
- <span data-ttu-id="aaaa9-571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-573">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-574">'Identity'</span></span>
- <span data-ttu-id="aaaa9-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-576">'Razor'</span></span>
- <span data-ttu-id="aaaa9-577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-577">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-579">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-580">'Identity'</span></span>
- <span data-ttu-id="aaaa9-581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-581">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-582">'Razor'</span></span>
- <span data-ttu-id="aaaa9-583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-585">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-586">'Identity'</span></span>
- <span data-ttu-id="aaaa9-587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-587">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-588">'Razor'</span></span>
- <span data-ttu-id="aaaa9-589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-591">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-592">'Identity'</span></span>
- <span data-ttu-id="aaaa9-593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-593">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-594">'Razor'</span></span>
- <span data-ttu-id="aaaa9-595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-595">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="aaaa9-597"> | `true` | | [AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-597"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="aaaa9-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="aaaa9-599">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-599">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-600">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-600">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="aaaa9-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="aaaa9-602">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-602">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="aaaa9-603">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-603">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="aaaa9-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 同時受け入れの最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="aaaa9-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 受け入れるコンカレント接続の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="aaaa9-606">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-606">Use `-1` for infinite.</span></span> <span data-ttu-id="aaaa9-607">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-607">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="aaaa9-608">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="aaaa9-608">(machine-wide</span></span><br><span data-ttu-id="aaaa9-609">設定) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-609">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="aaaa9-610">| 30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-610">| 30000000 bytes</span></span><br><span data-ttu-id="aaaa9-611">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-611">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="aaaa9-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="aaaa9-613">(正常に完了) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-613">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="aaaa9-614">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-614">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="aaaa9-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="aaaa9-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="aaaa9-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="aaaa9-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="aaaa9-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="aaaa9-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="aaaa9-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="aaaa9-622">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-622">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="aaaa9-623">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-623">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-624">|  |</span><span class="sxs-lookup"><span data-stu-id="aaaa9-624">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="aaaa9-625">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-625">**MaxRequestBodySize**</span></span>

<span data-ttu-id="aaaa9-626">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-626">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="aaaa9-627">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-627">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="aaaa9-628">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-628">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="aaaa9-629">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-629">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aaaa9-630">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-630">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="aaaa9-631">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-631">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aaaa9-632">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-632">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="aaaa9-633">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-633">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-634">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-634">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="aaaa9-635">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-635">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="aaaa9-637">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-637">Configure Windows Server</span></span>

1. <span data-ttu-id="aaaa9-638">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-638">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="aaaa9-639">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-639">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-640">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-640">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="aaaa9-641">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-641">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-642">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-642">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="aaaa9-643">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-643">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="aaaa9-644">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-644">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="aaaa9-645">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-645">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="aaaa9-646">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-646">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="aaaa9-647">**.NET Core**: アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-647">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="aaaa9-648">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-648">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="aaaa9-649">**.NET Framework**:アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-649">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="aaaa9-650">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-650">Install the required .NET Framework.</span></span> <span data-ttu-id="aaaa9-651">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-651">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="aaaa9-652">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-652">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="aaaa9-653">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-653">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="aaaa9-654">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-654">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="aaaa9-655">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-655">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="aaaa9-656">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-656">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="aaaa9-657">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-657">`urls` command-line argument</span></span>
   * <span data-ttu-id="aaaa9-658">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-658">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="aaaa9-659">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-659">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="aaaa9-660">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-660">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="aaaa9-661">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-661">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="aaaa9-662">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-662">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="aaaa9-663">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-663">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="aaaa9-664">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-664">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="aaaa9-665">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-665">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="aaaa9-666">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-666">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="aaaa9-667">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-667">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="aaaa9-668">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-668">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="aaaa9-669">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-669">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="aaaa9-670">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-670">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="aaaa9-671">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-671">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="aaaa9-672">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-672">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="aaaa9-673">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-673">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-674">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-674">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="aaaa9-675">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-675">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="aaaa9-676">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-676">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-677">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-677">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="aaaa9-678">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="aaaa9-678">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="aaaa9-679">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-679">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="aaaa9-680">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-680">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="aaaa9-681">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-681">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="aaaa9-682">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-682">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="aaaa9-683">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-683">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="aaaa9-684">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-684">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="aaaa9-685">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-685">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="aaaa9-686">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-686">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-687">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-687">Use a valid IP address.</span></span>
   * <span data-ttu-id="aaaa9-688">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-688">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="aaaa9-689">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-689">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="aaaa9-690">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-690">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="aaaa9-691">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-691">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="aaaa9-692">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-692">In Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-693">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-693">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="aaaa9-694">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-694">Select the **Package** tab.</span></span>
     * <span data-ttu-id="aaaa9-695">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-695">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="aaaa9-696">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-696">When not using Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-697">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-697">Open the app's project file.</span></span>
     * <span data-ttu-id="aaaa9-698">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-698">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="aaaa9-699">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-699">In the following example:</span></span>

   * <span data-ttu-id="aaaa9-700">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-700">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="aaaa9-701">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-701">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="aaaa9-702">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-702">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="aaaa9-703">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-703">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="aaaa9-704">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-704">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="aaaa9-705">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-705">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="aaaa9-706">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-706">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="aaaa9-707">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-707">Run the app.</span></span>

   <span data-ttu-id="aaaa9-708">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-708">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="aaaa9-709">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-709">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-710">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-710">The app responds at the server's public IP address.</span></span> <span data-ttu-id="aaaa9-711">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-711">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="aaaa9-712">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-712">A development certificate is used in this example.</span></span> <span data-ttu-id="aaaa9-713">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-713">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="aaaa9-715">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-715">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="aaaa9-716">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-716">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="aaaa9-717">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-717">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaaa9-718">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aaaa9-718">Additional resources</span></span>

* [<span data-ttu-id="aaaa9-719">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="aaaa9-719">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="aaaa9-720">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="aaaa9-720">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="aaaa9-721">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-721">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="aaaa9-722">ホスト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-722">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="aaaa9-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="aaaa9-724">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-724">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aaaa9-725">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-725">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-726">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-726">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="aaaa9-727">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-727">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="aaaa9-728">ポート共有</span><span class="sxs-lookup"><span data-stu-id="aaaa9-728">Port sharing</span></span>
* <span data-ttu-id="aaaa9-729">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="aaaa9-729">HTTPS with SNI</span></span>
* <span data-ttu-id="aaaa9-730">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-730">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="aaaa9-731">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="aaaa9-731">Direct file transmission</span></span>
* <span data-ttu-id="aaaa9-732">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-732">Response caching</span></span>
* <span data-ttu-id="aaaa9-733">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-733">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="aaaa9-734">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-734">Supported Windows versions:</span></span>

* <span data-ttu-id="aaaa9-735">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-735">Windows 7 or later</span></span>
* <span data-ttu-id="aaaa9-736">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-736">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="aaaa9-737">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-737">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="aaaa9-738">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="aaaa9-738">When to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-739">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-739">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="aaaa9-740">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-740">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="aaaa9-742">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-742">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="aaaa9-744">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-744">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="aaaa9-745">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-745">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="aaaa9-746">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="aaaa9-746">HTTP/2 support</span></span>

<span data-ttu-id="aaaa9-747">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-747">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aaaa9-748">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="aaaa9-748">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="aaaa9-749">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-749">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aaaa9-750">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="aaaa9-750">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aaaa9-751">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-751">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="aaaa9-752">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-752">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="aaaa9-753">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-753">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="aaaa9-754">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-754">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="aaaa9-755">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="aaaa9-755">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="aaaa9-756">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-756">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="aaaa9-757">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-757">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="aaaa9-758">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-758">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="aaaa9-759">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-759">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="aaaa9-760">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="aaaa9-760">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="aaaa9-761">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-761">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="aaaa9-762">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-762">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="aaaa9-763">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-763">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="aaaa9-764">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-764">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="aaaa9-765">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-765">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="aaaa9-766">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-766">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="aaaa9-767">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-767">**HTTP.sys options**</span></span>

| <span data-ttu-id="aaaa9-768">プロパティ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-768">Property</span></span> | <span data-ttu-id="aaaa9-769">説明</span><span class="sxs-lookup"><span data-stu-id="aaaa9-769">Description</span></span> | <span data-ttu-id="aaaa9-770">Default</span><span class="sxs-lookup"><span data-stu-id="aaaa9-770">Default</span></span> |
| ---
<span data-ttu-id="aaaa9-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-772">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-773">'Identity'</span></span>
- <span data-ttu-id="aaaa9-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-775">'Razor'</span></span>
- <span data-ttu-id="aaaa9-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-778">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-779">'Identity'</span></span>
- <span data-ttu-id="aaaa9-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-781">'Razor'</span></span>
- <span data-ttu-id="aaaa9-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-782">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-784">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-785">'Identity'</span></span>
- <span data-ttu-id="aaaa9-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-787">'Razor'</span></span>
- <span data-ttu-id="aaaa9-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-790">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-791">'Identity'</span></span>
- <span data-ttu-id="aaaa9-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-793">'Razor'</span></span>
- <span data-ttu-id="aaaa9-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aaaa9-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aaaa9-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-796">'Blazor'</span></span>
- <span data-ttu-id="aaaa9-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-797">'Identity'</span></span>
- <span data-ttu-id="aaaa9-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="aaaa9-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aaaa9-799">'Razor'</span></span>
- <span data-ttu-id="aaaa9-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-800">'SignalR' uid:</span></span> 

<span data-ttu-id="aaaa9-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="aaaa9-802"> | `true` | | [AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-802"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="aaaa9-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="aaaa9-804">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-804">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-805">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-805">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="aaaa9-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="aaaa9-807">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-807">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="aaaa9-808">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-808">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="aaaa9-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 同時受け入れの最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="aaaa9-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 受け入れるコンカレント接続の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="aaaa9-811">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-811">Use `-1` for infinite.</span></span> <span data-ttu-id="aaaa9-812">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-812">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="aaaa9-813">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="aaaa9-813">(machine-wide</span></span><br><span data-ttu-id="aaaa9-814">設定) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-814">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="aaaa9-815">| 30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-815">| 30000000 bytes</span></span><br><span data-ttu-id="aaaa9-816">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-816">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="aaaa9-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="aaaa9-818">(正常に完了) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-818">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="aaaa9-819">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-819">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="aaaa9-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="aaaa9-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="aaaa9-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="aaaa9-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="aaaa9-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="aaaa9-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="aaaa9-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="aaaa9-827">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-827">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="aaaa9-828">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-828">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="aaaa9-829">|  |</span><span class="sxs-lookup"><span data-stu-id="aaaa9-829">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="aaaa9-830">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="aaaa9-830">**MaxRequestBodySize**</span></span>

<span data-ttu-id="aaaa9-831">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-831">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="aaaa9-832">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-832">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="aaaa9-833">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-833">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="aaaa9-834">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-834">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aaaa9-835">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-835">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="aaaa9-836">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-836">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aaaa9-837">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-837">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="aaaa9-838">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-838">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="aaaa9-839">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-839">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="aaaa9-840">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-840">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="aaaa9-842">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="aaaa9-842">Configure Windows Server</span></span>

1. <span data-ttu-id="aaaa9-843">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-843">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="aaaa9-844">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-844">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-845">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-845">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="aaaa9-846">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-846">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="aaaa9-847">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-847">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="aaaa9-848">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-848">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="aaaa9-849">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-849">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="aaaa9-850">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-850">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="aaaa9-851">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-851">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="aaaa9-852">**.NET Core**: アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-852">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="aaaa9-853">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-853">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="aaaa9-854">**.NET Framework**:アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-854">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="aaaa9-855">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-855">Install the required .NET Framework.</span></span> <span data-ttu-id="aaaa9-856">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-856">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="aaaa9-857">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-857">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="aaaa9-858">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-858">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="aaaa9-859">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-859">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="aaaa9-860">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-860">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="aaaa9-861">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-861">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="aaaa9-862">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-862">`urls` command-line argument</span></span>
   * <span data-ttu-id="aaaa9-863">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="aaaa9-863">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="aaaa9-864">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-864">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="aaaa9-865">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-865">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="aaaa9-866">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-866">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="aaaa9-867">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-867">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="aaaa9-868">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-868">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="aaaa9-869">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-869">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="aaaa9-870">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-870">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="aaaa9-871">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-871">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="aaaa9-872">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-872">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="aaaa9-873">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-873">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="aaaa9-874">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-874">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="aaaa9-875">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-875">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="aaaa9-876">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-876">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="aaaa9-877">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-877">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="aaaa9-878">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-878">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-879">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-879">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="aaaa9-880">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-880">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="aaaa9-881">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-881">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-882">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-882">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="aaaa9-883">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="aaaa9-883">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="aaaa9-884">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-884">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="aaaa9-885">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-885">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="aaaa9-886">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-886">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="aaaa9-887">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-887">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="aaaa9-888">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-888">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="aaaa9-889">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-889">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="aaaa9-890">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-890">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="aaaa9-891">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-891">Don't use a wildcard binding.</span></span> <span data-ttu-id="aaaa9-892">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-892">Use a valid IP address.</span></span>
   * <span data-ttu-id="aaaa9-893">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-893">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="aaaa9-894">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-894">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="aaaa9-895">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-895">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="aaaa9-896">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-896">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="aaaa9-897">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-897">In Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-898">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-898">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="aaaa9-899">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-899">Select the **Package** tab.</span></span>
     * <span data-ttu-id="aaaa9-900">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-900">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="aaaa9-901">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="aaaa9-901">When not using Visual Studio:</span></span>
     * <span data-ttu-id="aaaa9-902">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-902">Open the app's project file.</span></span>
     * <span data-ttu-id="aaaa9-903">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-903">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="aaaa9-904">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-904">In the following example:</span></span>

   * <span data-ttu-id="aaaa9-905">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-905">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="aaaa9-906">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-906">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="aaaa9-907">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-907">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="aaaa9-908">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-908">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="aaaa9-909">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-909">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="aaaa9-910">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-910">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="aaaa9-911">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-911">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="aaaa9-912">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-912">Run the app.</span></span>

   <span data-ttu-id="aaaa9-913">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-913">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="aaaa9-914">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-914">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="aaaa9-915">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-915">The app responds at the server's public IP address.</span></span> <span data-ttu-id="aaaa9-916">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-916">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="aaaa9-917">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-917">A development certificate is used in this example.</span></span> <span data-ttu-id="aaaa9-918">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-918">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="aaaa9-920">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="aaaa9-920">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="aaaa9-921">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-921">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="aaaa9-922">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aaaa9-922">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaaa9-923">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aaaa9-923">Additional resources</span></span>

* [<span data-ttu-id="aaaa9-924">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="aaaa9-924">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="aaaa9-925">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="aaaa9-925">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="aaaa9-926">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="aaaa9-926">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="aaaa9-927">ホスト</span><span class="sxs-lookup"><span data-stu-id="aaaa9-927">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
