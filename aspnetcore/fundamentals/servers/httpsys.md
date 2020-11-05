---
title: ASP.NET Core での HTTP.sys Web サーバーの実装
author: rick-anderson
description: Windows 上の ASP.NET Core 用 Web サーバーである HTTP.sys について説明します。 HTTP.sys は、Http.sys カーネル モード ドライバーに基づいて構築された、IIS なしで直接インターネットに接続するために使用できる Kestrel の代替製品です。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/servers/httpsys
ms.openlocfilehash: ca8aa126a44ea417017f0be0372e818a95ad8413
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053749"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="63315-104">ASP.NET Core での HTTP.sys Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="63315-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="63315-105">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="63315-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="63315-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="63315-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="63315-107">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="63315-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63315-108">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="63315-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="63315-109">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="63315-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="63315-110">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="63315-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="63315-111">ポート共有</span><span class="sxs-lookup"><span data-stu-id="63315-111">Port sharing</span></span>
* <span data-ttu-id="63315-112">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="63315-112">HTTPS with SNI</span></span>
* <span data-ttu-id="63315-113">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="63315-114">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="63315-114">Direct file transmission</span></span>
* <span data-ttu-id="63315-115">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="63315-115">Response caching</span></span>
* <span data-ttu-id="63315-116">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="63315-117">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="63315-117">Supported Windows versions:</span></span>

* <span data-ttu-id="63315-118">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="63315-118">Windows 7 or later</span></span>
* <span data-ttu-id="63315-119">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="63315-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="63315-120">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="63315-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="63315-121">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="63315-121">When to use HTTP.sys</span></span>

<span data-ttu-id="63315-122">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="63315-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="63315-123">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="63315-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="63315-125">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="63315-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="63315-127">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="63315-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="63315-128">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="63315-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="63315-129">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="63315-129">HTTP/2 support</span></span>

<span data-ttu-id="63315-130">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="63315-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="63315-131">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="63315-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="63315-132">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="63315-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="63315-133">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="63315-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="63315-134">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="63315-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="63315-135">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="63315-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="63315-136">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="63315-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="63315-137">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="63315-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="63315-138">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="63315-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="63315-139">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="63315-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="63315-140">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="63315-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="63315-141">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="63315-142">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="63315-143">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="63315-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="63315-144">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="63315-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="63315-145">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="63315-146">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="63315-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="63315-147">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="63315-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="63315-148">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="63315-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="63315-149">プロパティ</span><span class="sxs-lookup"><span data-stu-id="63315-149">Property</span></span> | <span data-ttu-id="63315-150">説明</span><span class="sxs-lookup"><span data-stu-id="63315-150">Description</span></span> | <span data-ttu-id="63315-151">Default</span><span class="sxs-lookup"><span data-stu-id="63315-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="63315-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="63315-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="63315-153">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="63315-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="63315-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="63315-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="63315-155">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="63315-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="63315-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="63315-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="63315-157">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="63315-158">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="63315-159">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="63315-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="63315-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="63315-161">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="63315-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="63315-162">`Set-:::no-loc(Cookie):::`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="63315-162">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="63315-163">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="63315-164">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="63315-165">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="63315-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="63315-166">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="63315-167">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-167">Use `-1` for infinite.</span></span> <span data-ttu-id="63315-168">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="63315-169">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="63315-169">(machine-wide</span></span><br><span data-ttu-id="63315-170">設定)</span><span class="sxs-lookup"><span data-stu-id="63315-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="63315-171">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="63315-172">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="63315-172">30000000 bytes</span></span><br><span data-ttu-id="63315-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="63315-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="63315-174">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="63315-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="63315-175">1000</span><span class="sxs-lookup"><span data-stu-id="63315-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="63315-176">これは、サーバーで要求キューの作成と構成を行う必要があるかどうか、または既存のキューにアタッチする必要があるかを示します。</span><span class="sxs-lookup"><span data-stu-id="63315-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="63315-177">既存のキューにアタッチする場合、既存の構成オプションのほとんどは適用されません。</span><span class="sxs-lookup"><span data-stu-id="63315-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="63315-178">HTTP.sys 要求キューの名前。</span><span class="sxs-lookup"><span data-stu-id="63315-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="63315-179">`null` (匿名キュー)</span><span class="sxs-lookup"><span data-stu-id="63315-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="63315-180">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="63315-181">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="63315-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="63315-182">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="63315-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="63315-183">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="63315-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="63315-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="63315-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="63315-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="63315-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="63315-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="63315-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="63315-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="63315-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="63315-190">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="63315-191">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="63315-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="63315-192">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="63315-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="63315-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="63315-194">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="63315-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="63315-195">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="63315-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="63315-196">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="63315-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="63315-197">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="63315-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="63315-198">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="63315-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="63315-199">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="63315-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="63315-200">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="63315-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="63315-201">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="63315-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="63315-202">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="63315-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="63315-203">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="63315-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="63315-205">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="63315-205">Configure Windows Server</span></span>

1. <span data-ttu-id="63315-206">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="63315-207">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-208">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="63315-209">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-210">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="63315-211">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="63315-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="63315-212">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="63315-213">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="63315-214">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="63315-215">**.NET Core** : アプリで .NET Core が必要な場合は、 [.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-215">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="63315-216">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="63315-217">**.NET Framework** :アプリで .NET Framework が必要な場合は、 [.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-217">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="63315-218">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-218">Install the required .NET Framework.</span></span> <span data-ttu-id="63315-219">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="63315-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="63315-220">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="63315-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="63315-221">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="63315-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="63315-222">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="63315-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="63315-223">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="63315-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="63315-224">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="63315-225">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="63315-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="63315-226">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="63315-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="63315-227">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="63315-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="63315-228">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="63315-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="63315-229">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="63315-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="63315-230">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="63315-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="63315-231">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](/windows/win32/http/urlprefix-strings)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="63315-232">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="63315-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="63315-233">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="63315-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="63315-234">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="63315-235">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="63315-236">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="63315-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="63315-237">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="63315-238">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="63315-239">HTTP.sys を構成するための組み込みツールは、 *netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="63315-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="63315-240">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="63315-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="63315-241">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="63315-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="63315-242">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="63315-243">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="63315-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="63315-244">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-245">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="63315-246">" *URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="63315-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="63315-247">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="63315-248">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="63315-249">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="63315-250">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="63315-251">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="63315-252">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="63315-253">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="63315-254">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-255">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="63315-256">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="63315-257">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="63315-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="63315-258">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="63315-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="63315-259">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="63315-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="63315-260">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63315-260">In Visual Studio:</span></span>
     * <span data-ttu-id="63315-261">**ソリューション エクスプローラー** 内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="63315-262">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="63315-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="63315-263">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="63315-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="63315-264">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="63315-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="63315-265">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-265">Open the app's project file.</span></span>
     * <span data-ttu-id="63315-266">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="63315-267">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="63315-267">In the following example:</span></span>

   * <span data-ttu-id="63315-268">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="63315-269">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="63315-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="63315-270">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="63315-271">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="63315-272">以下は、 *netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="63315-272">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="63315-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10)) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="63315-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="63315-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="63315-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="63315-275">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-275">Run the app.</span></span>

   <span data-ttu-id="63315-276">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="63315-277">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="63315-278">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="63315-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="63315-279">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="63315-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="63315-280">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="63315-280">A development certificate is used in this example.</span></span> <span data-ttu-id="63315-281">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="63315-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="63315-283">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="63315-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="63315-284">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="63315-285">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="63315-286">gRPC をサポートする高度な HTTP/2 機能</span><span class="sxs-lookup"><span data-stu-id="63315-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="63315-287">HTTP.sys 内の追加の HTTP/2 機能によって、gRPC がサポートされています。これには、応答トレーラーやリセット フレームの送信に関するサポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="63315-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="63315-288">HTTP.SYS で gRPC を実行するための要件:</span><span class="sxs-lookup"><span data-stu-id="63315-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="63315-289">Windows 10、OS Build 19041.508 以降</span><span class="sxs-lookup"><span data-stu-id="63315-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="63315-290">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="63315-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="63315-291">予告編</span><span class="sxs-lookup"><span data-stu-id="63315-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="63315-292">Reset</span><span class="sxs-lookup"><span data-stu-id="63315-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="63315-293">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="63315-293">Additional resources</span></span>

* [<span data-ttu-id="63315-294">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="63315-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="63315-295">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="63315-295">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="63315-296">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="63315-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="63315-297">ホスト</span><span class="sxs-lookup"><span data-stu-id="63315-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="63315-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="63315-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="63315-299">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="63315-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63315-300">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="63315-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="63315-301">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="63315-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="63315-302">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="63315-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="63315-303">ポート共有</span><span class="sxs-lookup"><span data-stu-id="63315-303">Port sharing</span></span>
* <span data-ttu-id="63315-304">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="63315-304">HTTPS with SNI</span></span>
* <span data-ttu-id="63315-305">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="63315-306">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="63315-306">Direct file transmission</span></span>
* <span data-ttu-id="63315-307">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="63315-307">Response caching</span></span>
* <span data-ttu-id="63315-308">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="63315-309">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="63315-309">Supported Windows versions:</span></span>

* <span data-ttu-id="63315-310">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="63315-310">Windows 7 or later</span></span>
* <span data-ttu-id="63315-311">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="63315-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="63315-312">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="63315-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="63315-313">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="63315-313">When to use HTTP.sys</span></span>

<span data-ttu-id="63315-314">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="63315-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="63315-315">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="63315-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="63315-317">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="63315-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="63315-319">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="63315-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="63315-320">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="63315-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="63315-321">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="63315-321">HTTP/2 support</span></span>

<span data-ttu-id="63315-322">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="63315-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="63315-323">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="63315-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="63315-324">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="63315-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="63315-325">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="63315-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="63315-326">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="63315-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="63315-327">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="63315-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="63315-328">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="63315-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="63315-329">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="63315-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="63315-330">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="63315-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="63315-331">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="63315-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="63315-332">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="63315-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="63315-333">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="63315-334">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="63315-335">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="63315-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="63315-336">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="63315-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="63315-337">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="63315-338">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="63315-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="63315-339">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="63315-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="63315-340">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="63315-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="63315-341">プロパティ</span><span class="sxs-lookup"><span data-stu-id="63315-341">Property</span></span> | <span data-ttu-id="63315-342">説明</span><span class="sxs-lookup"><span data-stu-id="63315-342">Description</span></span> | <span data-ttu-id="63315-343">Default</span><span class="sxs-lookup"><span data-stu-id="63315-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="63315-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="63315-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="63315-345">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="63315-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="63315-346">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="63315-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="63315-347">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="63315-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="63315-348">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="63315-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="63315-349">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="63315-350">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="63315-351">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="63315-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="63315-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="63315-353">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="63315-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="63315-354">`Set-:::no-loc(Cookie):::`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="63315-354">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="63315-355">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="63315-356">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="63315-357">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="63315-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="63315-358">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="63315-359">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-359">Use `-1` for infinite.</span></span> <span data-ttu-id="63315-360">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="63315-361">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="63315-361">(machine-wide</span></span><br><span data-ttu-id="63315-362">設定)</span><span class="sxs-lookup"><span data-stu-id="63315-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="63315-363">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="63315-364">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="63315-364">30000000 bytes</span></span><br><span data-ttu-id="63315-365">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="63315-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="63315-366">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="63315-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="63315-367">1000</span><span class="sxs-lookup"><span data-stu-id="63315-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="63315-368">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="63315-369">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="63315-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="63315-370">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="63315-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="63315-371">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="63315-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="63315-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="63315-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="63315-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="63315-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="63315-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="63315-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="63315-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="63315-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="63315-378">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="63315-379">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="63315-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="63315-380">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="63315-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="63315-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="63315-382">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="63315-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="63315-383">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="63315-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="63315-384">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="63315-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="63315-385">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="63315-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="63315-386">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="63315-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="63315-387">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="63315-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="63315-388">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="63315-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="63315-389">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="63315-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="63315-390">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="63315-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="63315-391">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="63315-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="63315-393">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="63315-393">Configure Windows Server</span></span>

1. <span data-ttu-id="63315-394">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="63315-395">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-396">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="63315-397">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-398">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="63315-399">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="63315-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="63315-400">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="63315-401">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="63315-402">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="63315-403">**.NET Core** : アプリで .NET Core が必要な場合は、 [.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-403">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="63315-404">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="63315-405">**.NET Framework** :アプリで .NET Framework が必要な場合は、 [.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-405">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="63315-406">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-406">Install the required .NET Framework.</span></span> <span data-ttu-id="63315-407">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="63315-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="63315-408">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="63315-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="63315-409">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="63315-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="63315-410">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="63315-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="63315-411">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="63315-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="63315-412">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="63315-413">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="63315-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="63315-414">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="63315-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="63315-415">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="63315-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="63315-416">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="63315-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="63315-417">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="63315-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="63315-418">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="63315-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="63315-419">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](/windows/win32/http/urlprefix-strings)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="63315-420">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="63315-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="63315-421">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="63315-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="63315-422">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="63315-423">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="63315-424">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="63315-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="63315-425">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="63315-426">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="63315-427">HTTP.sys を構成するための組み込みツールは、 *netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="63315-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="63315-428">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="63315-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="63315-429">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="63315-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="63315-430">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="63315-431">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="63315-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="63315-432">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-433">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="63315-434">" *URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="63315-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="63315-435">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="63315-436">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="63315-437">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="63315-438">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="63315-439">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="63315-440">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="63315-441">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="63315-442">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-443">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="63315-444">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="63315-445">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="63315-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="63315-446">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="63315-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="63315-447">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="63315-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="63315-448">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63315-448">In Visual Studio:</span></span>
     * <span data-ttu-id="63315-449">**ソリューション エクスプローラー** 内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="63315-450">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="63315-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="63315-451">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="63315-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="63315-452">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="63315-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="63315-453">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-453">Open the app's project file.</span></span>
     * <span data-ttu-id="63315-454">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="63315-455">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="63315-455">In the following example:</span></span>

   * <span data-ttu-id="63315-456">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="63315-457">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="63315-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="63315-458">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="63315-459">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="63315-460">以下は、 *netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="63315-460">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="63315-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10)) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="63315-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="63315-462">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="63315-462">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="63315-463">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-463">Run the app.</span></span>

   <span data-ttu-id="63315-464">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="63315-465">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="63315-466">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="63315-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="63315-467">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="63315-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="63315-468">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="63315-468">A development certificate is used in this example.</span></span> <span data-ttu-id="63315-469">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="63315-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="63315-471">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="63315-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="63315-472">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="63315-473">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63315-474">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="63315-474">Additional resources</span></span>

* [<span data-ttu-id="63315-475">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="63315-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="63315-476">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="63315-476">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="63315-477">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="63315-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="63315-478">ホスト</span><span class="sxs-lookup"><span data-stu-id="63315-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="63315-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="63315-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="63315-480">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="63315-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63315-481">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="63315-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="63315-482">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="63315-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="63315-483">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="63315-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="63315-484">ポート共有</span><span class="sxs-lookup"><span data-stu-id="63315-484">Port sharing</span></span>
* <span data-ttu-id="63315-485">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="63315-485">HTTPS with SNI</span></span>
* <span data-ttu-id="63315-486">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="63315-487">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="63315-487">Direct file transmission</span></span>
* <span data-ttu-id="63315-488">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="63315-488">Response caching</span></span>
* <span data-ttu-id="63315-489">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="63315-490">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="63315-490">Supported Windows versions:</span></span>

* <span data-ttu-id="63315-491">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="63315-491">Windows 7 or later</span></span>
* <span data-ttu-id="63315-492">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="63315-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="63315-493">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="63315-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="63315-494">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="63315-494">When to use HTTP.sys</span></span>

<span data-ttu-id="63315-495">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="63315-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="63315-496">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="63315-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="63315-498">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="63315-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="63315-500">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="63315-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="63315-501">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="63315-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="63315-502">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="63315-502">HTTP/2 support</span></span>

<span data-ttu-id="63315-503">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="63315-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="63315-504">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="63315-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="63315-505">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="63315-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="63315-506">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="63315-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="63315-507">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="63315-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="63315-508">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="63315-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="63315-509">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="63315-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="63315-510">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="63315-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="63315-511">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="63315-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="63315-512">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="63315-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="63315-513">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="63315-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="63315-514">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="63315-515">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="63315-516">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="63315-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="63315-517">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="63315-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="63315-518">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="63315-519">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="63315-520">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="63315-521">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="63315-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="63315-522">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="63315-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="63315-523">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="63315-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="63315-524">プロパティ</span><span class="sxs-lookup"><span data-stu-id="63315-524">Property</span></span> | <span data-ttu-id="63315-525">説明</span><span class="sxs-lookup"><span data-stu-id="63315-525">Description</span></span> | <span data-ttu-id="63315-526">Default</span><span class="sxs-lookup"><span data-stu-id="63315-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="63315-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="63315-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="63315-528">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="63315-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="63315-529">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="63315-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="63315-530">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="63315-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="63315-531">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="63315-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="63315-532">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="63315-533">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="63315-534">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="63315-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="63315-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="63315-536">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="63315-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="63315-537">`Set-:::no-loc(Cookie):::`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="63315-537">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="63315-538">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="63315-539">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="63315-540">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="63315-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="63315-541">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="63315-542">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-542">Use `-1` for infinite.</span></span> <span data-ttu-id="63315-543">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="63315-544">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="63315-544">(machine-wide</span></span><br><span data-ttu-id="63315-545">設定)</span><span class="sxs-lookup"><span data-stu-id="63315-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="63315-546">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="63315-547">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="63315-547">30000000 bytes</span></span><br><span data-ttu-id="63315-548">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="63315-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="63315-549">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="63315-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="63315-550">1000</span><span class="sxs-lookup"><span data-stu-id="63315-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="63315-551">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="63315-552">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="63315-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="63315-553">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="63315-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="63315-554">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="63315-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="63315-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="63315-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="63315-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="63315-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="63315-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="63315-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="63315-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="63315-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="63315-561">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="63315-562">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="63315-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="63315-563">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="63315-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="63315-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="63315-565">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="63315-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="63315-566">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="63315-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="63315-567">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="63315-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="63315-568">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="63315-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="63315-569">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="63315-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="63315-570">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="63315-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="63315-571">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="63315-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="63315-572">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="63315-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="63315-573">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="63315-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="63315-574">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="63315-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="63315-576">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="63315-576">Configure Windows Server</span></span>

1. <span data-ttu-id="63315-577">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="63315-578">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-579">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="63315-580">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-581">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="63315-582">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="63315-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="63315-583">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="63315-584">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="63315-585">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="63315-586">**.NET Core** : アプリで .NET Core が必要な場合は、 [.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-586">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="63315-587">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="63315-588">**.NET Framework** :アプリで .NET Framework が必要な場合は、 [.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-588">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="63315-589">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-589">Install the required .NET Framework.</span></span> <span data-ttu-id="63315-590">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="63315-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="63315-591">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="63315-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="63315-592">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="63315-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="63315-593">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="63315-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="63315-594">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="63315-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="63315-595">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="63315-596">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="63315-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="63315-597">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="63315-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="63315-598">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="63315-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="63315-599">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="63315-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="63315-600">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="63315-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="63315-601">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="63315-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="63315-602">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](/windows/win32/http/urlprefix-strings)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="63315-603">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="63315-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="63315-604">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="63315-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="63315-605">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="63315-606">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="63315-607">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="63315-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="63315-608">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="63315-609">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="63315-610">HTTP.sys を構成するための組み込みツールは、 *netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="63315-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="63315-611">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="63315-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="63315-612">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="63315-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="63315-613">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="63315-614">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="63315-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="63315-615">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-616">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="63315-617">" *URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="63315-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="63315-618">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="63315-619">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="63315-620">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="63315-621">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="63315-622">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="63315-623">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="63315-624">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="63315-625">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-626">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="63315-627">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="63315-628">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="63315-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="63315-629">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="63315-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="63315-630">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="63315-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="63315-631">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63315-631">In Visual Studio:</span></span>
     * <span data-ttu-id="63315-632">**ソリューション エクスプローラー** 内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="63315-633">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="63315-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="63315-634">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="63315-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="63315-635">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="63315-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="63315-636">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-636">Open the app's project file.</span></span>
     * <span data-ttu-id="63315-637">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="63315-638">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="63315-638">In the following example:</span></span>

   * <span data-ttu-id="63315-639">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="63315-640">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="63315-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="63315-641">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="63315-642">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="63315-643">以下は、 *netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="63315-643">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="63315-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10)) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="63315-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="63315-645">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="63315-645">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="63315-646">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-646">Run the app.</span></span>

   <span data-ttu-id="63315-647">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="63315-648">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="63315-649">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="63315-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="63315-650">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="63315-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="63315-651">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="63315-651">A development certificate is used in this example.</span></span> <span data-ttu-id="63315-652">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="63315-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="63315-654">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="63315-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="63315-655">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="63315-656">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63315-657">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="63315-657">Additional resources</span></span>

* [<span data-ttu-id="63315-658">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="63315-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="63315-659">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="63315-659">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="63315-660">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="63315-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="63315-661">ホスト</span><span class="sxs-lookup"><span data-stu-id="63315-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="63315-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="63315-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="63315-663">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="63315-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63315-664">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="63315-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="63315-665">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="63315-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="63315-666">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="63315-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="63315-667">ポート共有</span><span class="sxs-lookup"><span data-stu-id="63315-667">Port sharing</span></span>
* <span data-ttu-id="63315-668">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="63315-668">HTTPS with SNI</span></span>
* <span data-ttu-id="63315-669">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="63315-670">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="63315-670">Direct file transmission</span></span>
* <span data-ttu-id="63315-671">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="63315-671">Response caching</span></span>
* <span data-ttu-id="63315-672">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="63315-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="63315-673">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="63315-673">Supported Windows versions:</span></span>

* <span data-ttu-id="63315-674">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="63315-674">Windows 7 or later</span></span>
* <span data-ttu-id="63315-675">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="63315-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="63315-676">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="63315-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="63315-677">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="63315-677">When to use HTTP.sys</span></span>

<span data-ttu-id="63315-678">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="63315-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="63315-679">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="63315-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="63315-681">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="63315-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="63315-683">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="63315-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="63315-684">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="63315-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="63315-685">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="63315-685">HTTP/2 support</span></span>

<span data-ttu-id="63315-686">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="63315-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="63315-687">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="63315-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="63315-688">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="63315-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="63315-689">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="63315-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="63315-690">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="63315-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="63315-691">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="63315-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="63315-692">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="63315-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="63315-693">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="63315-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="63315-694">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="63315-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="63315-695">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="63315-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="63315-696">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="63315-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="63315-697">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="63315-698">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="63315-699">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="63315-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="63315-700">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="63315-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="63315-701">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="63315-702">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="63315-703">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="63315-704">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="63315-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="63315-705">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="63315-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="63315-706">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="63315-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="63315-707">プロパティ</span><span class="sxs-lookup"><span data-stu-id="63315-707">Property</span></span> | <span data-ttu-id="63315-708">説明</span><span class="sxs-lookup"><span data-stu-id="63315-708">Description</span></span> | <span data-ttu-id="63315-709">Default</span><span class="sxs-lookup"><span data-stu-id="63315-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="63315-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="63315-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="63315-711">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="63315-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="63315-712">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="63315-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="63315-713">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="63315-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="63315-714">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="63315-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="63315-715">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="63315-716">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="63315-717">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="63315-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="63315-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="63315-719">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="63315-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="63315-720">`Set-:::no-loc(Cookie):::`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="63315-720">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="63315-721">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="63315-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="63315-722">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="63315-723">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="63315-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="63315-724">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="63315-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="63315-725">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-725">Use `-1` for infinite.</span></span> <span data-ttu-id="63315-726">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="63315-727">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="63315-727">(machine-wide</span></span><br><span data-ttu-id="63315-728">設定)</span><span class="sxs-lookup"><span data-stu-id="63315-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="63315-729">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="63315-730">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="63315-730">30000000 bytes</span></span><br><span data-ttu-id="63315-731">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="63315-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="63315-732">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="63315-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="63315-733">1000</span><span class="sxs-lookup"><span data-stu-id="63315-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="63315-734">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="63315-735">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="63315-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="63315-736">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="63315-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="63315-737">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="63315-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody):HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="63315-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="63315-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody):要求のエンティティ本体が到着するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="63315-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait):HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="63315-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection):アイドル接続で許容される時間。</span><span class="sxs-lookup"><span data-stu-id="63315-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="63315-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond):応答の最小送信レート。</span><span class="sxs-lookup"><span data-stu-id="63315-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="63315-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue):要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間。</span><span class="sxs-lookup"><span data-stu-id="63315-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="63315-744">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="63315-745">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="63315-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="63315-746">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="63315-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="63315-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="63315-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="63315-748">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="63315-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="63315-749">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="63315-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="63315-750">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="63315-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="63315-751">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="63315-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="63315-752">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="63315-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="63315-753">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="63315-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="63315-754">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="63315-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="63315-755">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="63315-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="63315-756">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="63315-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="63315-757">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="63315-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="63315-759">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="63315-759">Configure Windows Server</span></span>

1. <span data-ttu-id="63315-760">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="63315-761">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-762">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="63315-763">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="63315-764">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="63315-765">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="63315-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="63315-766">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="63315-767">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="63315-768">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="63315-769">**.NET Core** : アプリで .NET Core が必要な場合は、 [.NET Core のダウンロード](https://dotnet.microsoft.com/download)から **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-769">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="63315-770">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="63315-771">**.NET Framework** :アプリで .NET Framework が必要な場合は、 [.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-771">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="63315-772">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="63315-772">Install the required .NET Framework.</span></span> <span data-ttu-id="63315-773">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="63315-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="63315-774">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="63315-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="63315-775">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="63315-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="63315-776">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="63315-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="63315-777">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="63315-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="63315-778">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="63315-779">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="63315-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="63315-780">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="63315-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="63315-781">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="63315-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="63315-782">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="63315-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="63315-783">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="63315-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="63315-784">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="63315-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="63315-785">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](/windows/win32/http/urlprefix-strings)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="63315-786">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="63315-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="63315-787">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="63315-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="63315-788">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="63315-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="63315-789">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="63315-790">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="63315-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="63315-791">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="63315-792">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="63315-793">HTTP.sys を構成するための組み込みツールは、 *netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="63315-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="63315-794">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="63315-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="63315-795">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="63315-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="63315-796">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="63315-797">`<URL>`:完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="63315-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="63315-798">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-799">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="63315-800">" *URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="63315-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="63315-801">`<USER>`:ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="63315-802">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="63315-803">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="63315-804">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="63315-805">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="63315-806">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="63315-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="63315-807">`<IP>`:バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="63315-808">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="63315-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="63315-809">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="63315-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="63315-810">`<PORT>`:バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="63315-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="63315-811">`<THUMBPRINT>`:X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="63315-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="63315-812">`<GUID>`:情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="63315-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="63315-813">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="63315-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="63315-814">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="63315-814">In Visual Studio:</span></span>
     * <span data-ttu-id="63315-815">**ソリューション エクスプローラー** 内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="63315-816">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="63315-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="63315-817">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="63315-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="63315-818">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="63315-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="63315-819">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="63315-819">Open the app's project file.</span></span>
     * <span data-ttu-id="63315-820">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="63315-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="63315-821">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="63315-821">In the following example:</span></span>

   * <span data-ttu-id="63315-822">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="63315-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="63315-823">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="63315-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="63315-824">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="63315-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="63315-825">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="63315-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="63315-826">以下は、 *netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="63315-826">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="63315-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10)) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="63315-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="63315-828">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="63315-828">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="63315-829">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-829">Run the app.</span></span>

   <span data-ttu-id="63315-830">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="63315-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="63315-831">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="63315-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="63315-832">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="63315-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="63315-833">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="63315-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="63315-834">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="63315-834">A development certificate is used in this example.</span></span> <span data-ttu-id="63315-835">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="63315-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="63315-837">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="63315-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="63315-838">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="63315-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="63315-839">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="63315-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63315-840">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="63315-840">Additional resources</span></span>

* [<span data-ttu-id="63315-841">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="63315-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="63315-842">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="63315-842">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="63315-843">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="63315-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="63315-844">ホスト</span><span class="sxs-lookup"><span data-stu-id="63315-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
