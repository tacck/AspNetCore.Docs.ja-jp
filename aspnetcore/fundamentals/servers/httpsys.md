---
title: ASP.NET Core での HTTP.sys Web サーバーの実装
author: rick-anderson
description: Windows 上の ASP.NET Core 用 Web サーバーである HTTP.sys について説明します。 HTTP.sys は、Http.sys カーネル モード ドライバーに基づいて構築された、IIS なしで直接インターネットに接続するために使用できる Kestrel の代替製品です。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 5ff5eed1c8ad6f8863fe16e0c76ab104658ddc0c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769872"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d33e8-104">ASP.NET Core での HTTP.sys Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="d33e8-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d33e8-105">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d33e8-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="d33e8-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d33e8-107">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d33e8-108">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-109">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d33e8-110">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d33e8-111">ポート共有</span><span class="sxs-lookup"><span data-stu-id="d33e8-111">Port sharing</span></span>
* <span data-ttu-id="d33e8-112">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="d33e8-112">HTTPS with SNI</span></span>
* <span data-ttu-id="d33e8-113">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d33e8-114">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="d33e8-114">Direct file transmission</span></span>
* <span data-ttu-id="d33e8-115">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d33e8-115">Response caching</span></span>
* <span data-ttu-id="d33e8-116">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d33e8-117">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="d33e8-117">Supported Windows versions:</span></span>

* <span data-ttu-id="d33e8-118">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-118">Windows 7 or later</span></span>
* <span data-ttu-id="d33e8-119">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d33e8-120">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d33e8-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d33e8-121">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d33e8-121">When to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-122">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d33e8-123">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="d33e8-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d33e8-125">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="d33e8-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d33e8-127">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d33e8-128">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d33e8-129">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d33e8-129">HTTP/2 support</span></span>

<span data-ttu-id="d33e8-130">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d33e8-131">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d33e8-132">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d33e8-133">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d33e8-134">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d33e8-135">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d33e8-136">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d33e8-137">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d33e8-138">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d33e8-139">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d33e8-140">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d33e8-141">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d33e8-142">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d33e8-143">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="d33e8-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d33e8-144">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-145">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d33e8-146">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="d33e8-147">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d33e8-148">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="d33e8-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="d33e8-149">プロパティ</span><span class="sxs-lookup"><span data-stu-id="d33e8-149">Property</span></span> | <span data-ttu-id="d33e8-150">説明</span><span class="sxs-lookup"><span data-stu-id="d33e8-150">Description</span></span> | <span data-ttu-id="d33e8-151">Default</span><span class="sxs-lookup"><span data-stu-id="d33e8-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d33e8-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d33e8-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d33e8-153">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="d33e8-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d33e8-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d33e8-155">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d33e8-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="d33e8-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d33e8-157">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d33e8-158">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d33e8-159">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d33e8-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d33e8-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d33e8-161">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d33e8-162">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d33e8-163">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d33e8-164">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d33e8-165">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d33e8-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d33e8-166">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d33e8-167">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-167">Use `-1` for infinite.</span></span> <span data-ttu-id="d33e8-168">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d33e8-169">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="d33e8-169">(machine-wide</span></span><br><span data-ttu-id="d33e8-170">設定)</span><span class="sxs-lookup"><span data-stu-id="d33e8-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d33e8-171">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d33e8-172">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="d33e8-172">30000000 bytes</span></span><br><span data-ttu-id="d33e8-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="d33e8-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d33e8-174">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="d33e8-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d33e8-175">1000</span><span class="sxs-lookup"><span data-stu-id="d33e8-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="d33e8-176">これは、サーバーで要求キューの作成と構成を行う必要があるかどうか、または既存のキューにアタッチする必要があるかを示します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="d33e8-177">既存のキューにアタッチする場合、既存の構成オプションのほとんどは適用されません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="d33e8-178">HTTP.sys 要求キューの名前。</span><span class="sxs-lookup"><span data-stu-id="d33e8-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="d33e8-179">`null` (匿名キュー)</span><span class="sxs-lookup"><span data-stu-id="d33e8-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d33e8-180">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d33e8-181">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="d33e8-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d33e8-182">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d33e8-183">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d33e8-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d33e8-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d33e8-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d33e8-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d33e8-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d33e8-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d33e8-190">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d33e8-191">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d33e8-192">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d33e8-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d33e8-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d33e8-194">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d33e8-195">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d33e8-196">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d33e8-197">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d33e8-198">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d33e8-199">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d33e8-200">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="d33e8-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d33e8-201">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-202">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d33e8-203">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d33e8-205">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-205">Configure Windows Server</span></span>

1. <span data-ttu-id="d33e8-206">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d33e8-207">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-208">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d33e8-209">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-210">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d33e8-211">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d33e8-212">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d33e8-213">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d33e8-214">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d33e8-215">**.NET Core** &ndash; アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-215">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d33e8-216">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d33e8-217">**.NET Framework** &ndash; アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-217">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d33e8-218">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-218">Install the required .NET Framework.</span></span> <span data-ttu-id="d33e8-219">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d33e8-220">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d33e8-221">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d33e8-222">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d33e8-223">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d33e8-224">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d33e8-225">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d33e8-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="d33e8-226">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d33e8-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d33e8-227">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="d33e8-228">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d33e8-229">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d33e8-230">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d33e8-231">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d33e8-232">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d33e8-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d33e8-233">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d33e8-234">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d33e8-235">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d33e8-236">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d33e8-237">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d33e8-238">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d33e8-239">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d33e8-240">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d33e8-241">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d33e8-242">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d33e8-243">`<URL>` &ndash; 完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-243">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d33e8-244">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-245">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d33e8-246">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="d33e8-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d33e8-247">`<USER>` &ndash; ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-247">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d33e8-248">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d33e8-249">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d33e8-250">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d33e8-251">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d33e8-252">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d33e8-253">`<IP>` &ndash; バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-253">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d33e8-254">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-255">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="d33e8-256">`<PORT>` &ndash; バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-256">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d33e8-257">`<THUMBPRINT>` &ndash; X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-257">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d33e8-258">`<GUID>` &ndash; 情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-258">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d33e8-259">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d33e8-260">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d33e8-260">In Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-261">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d33e8-262">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d33e8-263">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d33e8-264">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="d33e8-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-265">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-265">Open the app's project file.</span></span>
     * <span data-ttu-id="d33e8-266">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d33e8-267">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-267">In the following example:</span></span>

   * <span data-ttu-id="d33e8-268">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d33e8-269">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d33e8-270">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d33e8-271">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d33e8-272">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d33e8-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="d33e8-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="d33e8-274">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="d33e8-274">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="d33e8-275">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-275">Run the app.</span></span>

   <span data-ttu-id="d33e8-276">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d33e8-277">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d33e8-278">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d33e8-279">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d33e8-280">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-280">A development certificate is used in this example.</span></span> <span data-ttu-id="d33e8-281">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d33e8-283">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d33e8-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d33e8-284">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d33e8-285">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d33e8-286">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d33e8-286">Additional resources</span></span>

* [<span data-ttu-id="d33e8-287">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="d33e8-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d33e8-288">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="d33e8-288">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="d33e8-289">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="d33e8-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d33e8-290">ホスト</span><span class="sxs-lookup"><span data-stu-id="d33e8-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="d33e8-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d33e8-292">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d33e8-293">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-294">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d33e8-295">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d33e8-296">ポート共有</span><span class="sxs-lookup"><span data-stu-id="d33e8-296">Port sharing</span></span>
* <span data-ttu-id="d33e8-297">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="d33e8-297">HTTPS with SNI</span></span>
* <span data-ttu-id="d33e8-298">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d33e8-299">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="d33e8-299">Direct file transmission</span></span>
* <span data-ttu-id="d33e8-300">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d33e8-300">Response caching</span></span>
* <span data-ttu-id="d33e8-301">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d33e8-302">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="d33e8-302">Supported Windows versions:</span></span>

* <span data-ttu-id="d33e8-303">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-303">Windows 7 or later</span></span>
* <span data-ttu-id="d33e8-304">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d33e8-305">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d33e8-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d33e8-306">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d33e8-306">When to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-307">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d33e8-308">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="d33e8-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d33e8-310">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="d33e8-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d33e8-312">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d33e8-313">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d33e8-314">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d33e8-314">HTTP/2 support</span></span>

<span data-ttu-id="d33e8-315">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d33e8-316">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d33e8-317">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d33e8-318">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d33e8-319">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d33e8-320">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d33e8-321">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d33e8-322">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d33e8-323">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d33e8-324">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d33e8-325">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d33e8-326">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d33e8-327">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d33e8-328">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="d33e8-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d33e8-329">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-330">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d33e8-331">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="d33e8-332">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d33e8-333">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="d33e8-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="d33e8-334">プロパティ</span><span class="sxs-lookup"><span data-stu-id="d33e8-334">Property</span></span> | <span data-ttu-id="d33e8-335">説明</span><span class="sxs-lookup"><span data-stu-id="d33e8-335">Description</span></span> | <span data-ttu-id="d33e8-336">Default</span><span class="sxs-lookup"><span data-stu-id="d33e8-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d33e8-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d33e8-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d33e8-338">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="d33e8-339">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d33e8-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d33e8-340">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d33e8-341">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="d33e8-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d33e8-342">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d33e8-343">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d33e8-344">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d33e8-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d33e8-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d33e8-346">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d33e8-347">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d33e8-348">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d33e8-349">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d33e8-350">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d33e8-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d33e8-351">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d33e8-352">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-352">Use `-1` for infinite.</span></span> <span data-ttu-id="d33e8-353">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d33e8-354">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="d33e8-354">(machine-wide</span></span><br><span data-ttu-id="d33e8-355">設定)</span><span class="sxs-lookup"><span data-stu-id="d33e8-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d33e8-356">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d33e8-357">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="d33e8-357">30000000 bytes</span></span><br><span data-ttu-id="d33e8-358">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="d33e8-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d33e8-359">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="d33e8-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d33e8-360">1000</span><span class="sxs-lookup"><span data-stu-id="d33e8-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d33e8-361">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d33e8-362">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="d33e8-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d33e8-363">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d33e8-364">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d33e8-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d33e8-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d33e8-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d33e8-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d33e8-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d33e8-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d33e8-371">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d33e8-372">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d33e8-373">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d33e8-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d33e8-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d33e8-375">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d33e8-376">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d33e8-377">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d33e8-378">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d33e8-379">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d33e8-380">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d33e8-381">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="d33e8-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d33e8-382">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-383">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d33e8-384">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d33e8-386">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-386">Configure Windows Server</span></span>

1. <span data-ttu-id="d33e8-387">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d33e8-388">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-389">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d33e8-390">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-391">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d33e8-392">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d33e8-393">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d33e8-394">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d33e8-395">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d33e8-396">**.NET Core** &ndash; アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-396">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d33e8-397">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d33e8-398">**.NET Framework** &ndash; アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-398">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d33e8-399">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-399">Install the required .NET Framework.</span></span> <span data-ttu-id="d33e8-400">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d33e8-401">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d33e8-402">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d33e8-403">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d33e8-404">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d33e8-405">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d33e8-406">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d33e8-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="d33e8-407">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d33e8-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d33e8-408">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="d33e8-409">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d33e8-410">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d33e8-411">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d33e8-412">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d33e8-413">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d33e8-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d33e8-414">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d33e8-415">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d33e8-416">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d33e8-417">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d33e8-418">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d33e8-419">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d33e8-420">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d33e8-421">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d33e8-422">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d33e8-423">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d33e8-424">`<URL>` &ndash; 完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-424">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d33e8-425">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-426">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d33e8-427">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="d33e8-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d33e8-428">`<USER>` &ndash; ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-428">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d33e8-429">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d33e8-430">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d33e8-431">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d33e8-432">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d33e8-433">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d33e8-434">`<IP>` &ndash; バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-434">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d33e8-435">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-436">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="d33e8-437">`<PORT>` &ndash; バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-437">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d33e8-438">`<THUMBPRINT>` &ndash; X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-438">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d33e8-439">`<GUID>` &ndash; 情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-439">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d33e8-440">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d33e8-441">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d33e8-441">In Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-442">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d33e8-443">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d33e8-444">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d33e8-445">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="d33e8-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-446">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-446">Open the app's project file.</span></span>
     * <span data-ttu-id="d33e8-447">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d33e8-448">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-448">In the following example:</span></span>

   * <span data-ttu-id="d33e8-449">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d33e8-450">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d33e8-451">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d33e8-452">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d33e8-453">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-453">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d33e8-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="d33e8-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="d33e8-455">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="d33e8-455">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="d33e8-456">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-456">Run the app.</span></span>

   <span data-ttu-id="d33e8-457">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d33e8-458">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d33e8-459">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d33e8-460">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d33e8-461">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-461">A development certificate is used in this example.</span></span> <span data-ttu-id="d33e8-462">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d33e8-464">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d33e8-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d33e8-465">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d33e8-466">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d33e8-467">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d33e8-467">Additional resources</span></span>

* [<span data-ttu-id="d33e8-468">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="d33e8-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d33e8-469">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="d33e8-469">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="d33e8-470">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="d33e8-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d33e8-471">ホスト</span><span class="sxs-lookup"><span data-stu-id="d33e8-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d33e8-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d33e8-473">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d33e8-474">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-475">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d33e8-476">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d33e8-477">ポート共有</span><span class="sxs-lookup"><span data-stu-id="d33e8-477">Port sharing</span></span>
* <span data-ttu-id="d33e8-478">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="d33e8-478">HTTPS with SNI</span></span>
* <span data-ttu-id="d33e8-479">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d33e8-480">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="d33e8-480">Direct file transmission</span></span>
* <span data-ttu-id="d33e8-481">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d33e8-481">Response caching</span></span>
* <span data-ttu-id="d33e8-482">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d33e8-483">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="d33e8-483">Supported Windows versions:</span></span>

* <span data-ttu-id="d33e8-484">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-484">Windows 7 or later</span></span>
* <span data-ttu-id="d33e8-485">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d33e8-486">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d33e8-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d33e8-487">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d33e8-487">When to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-488">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d33e8-489">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="d33e8-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d33e8-491">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="d33e8-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d33e8-493">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d33e8-494">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d33e8-495">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d33e8-495">HTTP/2 support</span></span>

<span data-ttu-id="d33e8-496">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d33e8-497">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d33e8-498">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d33e8-499">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d33e8-500">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d33e8-501">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d33e8-502">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d33e8-503">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d33e8-504">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d33e8-505">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d33e8-506">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d33e8-507">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d33e8-508">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d33e8-509">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="d33e8-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d33e8-510">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-511">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="d33e8-512">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="d33e8-513">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d33e8-514">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="d33e8-515">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d33e8-516">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="d33e8-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="d33e8-517">プロパティ</span><span class="sxs-lookup"><span data-stu-id="d33e8-517">Property</span></span> | <span data-ttu-id="d33e8-518">説明</span><span class="sxs-lookup"><span data-stu-id="d33e8-518">Description</span></span> | <span data-ttu-id="d33e8-519">Default</span><span class="sxs-lookup"><span data-stu-id="d33e8-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d33e8-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d33e8-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d33e8-521">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="d33e8-522">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d33e8-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d33e8-523">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d33e8-524">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="d33e8-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d33e8-525">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d33e8-526">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d33e8-527">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d33e8-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d33e8-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d33e8-529">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d33e8-530">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d33e8-531">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d33e8-532">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d33e8-533">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d33e8-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d33e8-534">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d33e8-535">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-535">Use `-1` for infinite.</span></span> <span data-ttu-id="d33e8-536">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d33e8-537">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="d33e8-537">(machine-wide</span></span><br><span data-ttu-id="d33e8-538">設定)</span><span class="sxs-lookup"><span data-stu-id="d33e8-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d33e8-539">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d33e8-540">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="d33e8-540">30000000 bytes</span></span><br><span data-ttu-id="d33e8-541">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="d33e8-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d33e8-542">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="d33e8-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d33e8-543">1000</span><span class="sxs-lookup"><span data-stu-id="d33e8-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d33e8-544">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d33e8-545">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="d33e8-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d33e8-546">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d33e8-547">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d33e8-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d33e8-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d33e8-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d33e8-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d33e8-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d33e8-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d33e8-554">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d33e8-555">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d33e8-556">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d33e8-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d33e8-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d33e8-558">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d33e8-559">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d33e8-560">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d33e8-561">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d33e8-562">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d33e8-563">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d33e8-564">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="d33e8-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d33e8-565">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-566">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d33e8-567">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d33e8-569">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-569">Configure Windows Server</span></span>

1. <span data-ttu-id="d33e8-570">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d33e8-571">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-572">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d33e8-573">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-574">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d33e8-575">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d33e8-576">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d33e8-577">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d33e8-578">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d33e8-579">**.NET Core** &ndash; アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-579">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d33e8-580">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d33e8-581">**.NET Framework** &ndash; アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-581">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d33e8-582">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-582">Install the required .NET Framework.</span></span> <span data-ttu-id="d33e8-583">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d33e8-584">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d33e8-585">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d33e8-586">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d33e8-587">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d33e8-588">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d33e8-589">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d33e8-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="d33e8-590">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d33e8-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d33e8-591">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="d33e8-592">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d33e8-593">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d33e8-594">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d33e8-595">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d33e8-596">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d33e8-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d33e8-597">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d33e8-598">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d33e8-599">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d33e8-600">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d33e8-601">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d33e8-602">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d33e8-603">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d33e8-604">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d33e8-605">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d33e8-606">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d33e8-607">`<URL>` &ndash; 完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-607">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d33e8-608">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-609">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d33e8-610">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="d33e8-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d33e8-611">`<USER>` &ndash; ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-611">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d33e8-612">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d33e8-613">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d33e8-614">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d33e8-615">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d33e8-616">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d33e8-617">`<IP>` &ndash; バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-617">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d33e8-618">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-619">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="d33e8-620">`<PORT>` &ndash; バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-620">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d33e8-621">`<THUMBPRINT>` &ndash; X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-621">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d33e8-622">`<GUID>` &ndash; 情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-622">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d33e8-623">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d33e8-624">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d33e8-624">In Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-625">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d33e8-626">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d33e8-627">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d33e8-628">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="d33e8-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-629">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-629">Open the app's project file.</span></span>
     * <span data-ttu-id="d33e8-630">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d33e8-631">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-631">In the following example:</span></span>

   * <span data-ttu-id="d33e8-632">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d33e8-633">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d33e8-634">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d33e8-635">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d33e8-636">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-636">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d33e8-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="d33e8-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="d33e8-638">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="d33e8-638">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="d33e8-639">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-639">Run the app.</span></span>

   <span data-ttu-id="d33e8-640">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d33e8-641">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d33e8-642">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d33e8-643">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d33e8-644">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-644">A development certificate is used in this example.</span></span> <span data-ttu-id="d33e8-645">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d33e8-647">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d33e8-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d33e8-648">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d33e8-649">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d33e8-650">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d33e8-650">Additional resources</span></span>

* [<span data-ttu-id="d33e8-651">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="d33e8-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d33e8-652">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="d33e8-652">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="d33e8-653">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="d33e8-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d33e8-654">ホスト</span><span class="sxs-lookup"><span data-stu-id="d33e8-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d33e8-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="d33e8-656">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d33e8-657">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-658">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="d33e8-659">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="d33e8-660">ポート共有</span><span class="sxs-lookup"><span data-stu-id="d33e8-660">Port sharing</span></span>
* <span data-ttu-id="d33e8-661">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="d33e8-661">HTTPS with SNI</span></span>
* <span data-ttu-id="d33e8-662">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="d33e8-663">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="d33e8-663">Direct file transmission</span></span>
* <span data-ttu-id="d33e8-664">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d33e8-664">Response caching</span></span>
* <span data-ttu-id="d33e8-665">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="d33e8-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="d33e8-666">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="d33e8-666">Supported Windows versions:</span></span>

* <span data-ttu-id="d33e8-667">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-667">Windows 7 or later</span></span>
* <span data-ttu-id="d33e8-668">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d33e8-669">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d33e8-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="d33e8-670">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d33e8-670">When to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-671">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="d33e8-672">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="d33e8-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="d33e8-674">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="d33e8-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="d33e8-676">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="d33e8-677">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d33e8-678">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d33e8-678">HTTP/2 support</span></span>

<span data-ttu-id="d33e8-679">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d33e8-680">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="d33e8-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="d33e8-681">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d33e8-682">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d33e8-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d33e8-683">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d33e8-684">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d33e8-685">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="d33e8-686">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="d33e8-687">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="d33e8-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="d33e8-688">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="d33e8-689">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="d33e8-690">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="d33e8-691">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="d33e8-692">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="d33e8-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="d33e8-693">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="d33e8-694">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="d33e8-695">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="d33e8-696">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="d33e8-697">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="d33e8-698">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="d33e8-699">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="d33e8-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="d33e8-700">プロパティ</span><span class="sxs-lookup"><span data-stu-id="d33e8-700">Property</span></span> | <span data-ttu-id="d33e8-701">説明</span><span class="sxs-lookup"><span data-stu-id="d33e8-701">Description</span></span> | <span data-ttu-id="d33e8-702">Default</span><span class="sxs-lookup"><span data-stu-id="d33e8-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="d33e8-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="d33e8-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="d33e8-704">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="d33e8-705">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="d33e8-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="d33e8-706">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="d33e8-707">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="d33e8-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="d33e8-708">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="d33e8-709">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="d33e8-710">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="d33e8-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="d33e8-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="d33e8-712">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="d33e8-713">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="d33e8-714">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="d33e8-715">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="d33e8-716">5 &times;[Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="d33e8-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="d33e8-717">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="d33e8-718">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-718">Use `-1` for infinite.</span></span> <span data-ttu-id="d33e8-719">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="d33e8-720">(コンピューター全体の</span><span class="sxs-lookup"><span data-stu-id="d33e8-720">(machine-wide</span></span><br><span data-ttu-id="d33e8-721">設定)</span><span class="sxs-lookup"><span data-stu-id="d33e8-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="d33e8-722">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="d33e8-723">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="d33e8-723">30000000 bytes</span></span><br><span data-ttu-id="d33e8-724">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="d33e8-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="d33e8-725">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="d33e8-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="d33e8-726">1000</span><span class="sxs-lookup"><span data-stu-id="d33e8-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="d33e8-727">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="d33e8-728">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="d33e8-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="d33e8-729">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="d33e8-730">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="d33e8-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="d33e8-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="d33e8-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="d33e8-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="d33e8-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="d33e8-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="d33e8-737">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="d33e8-738">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="d33e8-739">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="d33e8-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="d33e8-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="d33e8-741">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="d33e8-742">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="d33e8-743">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="d33e8-744">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d33e8-745">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="d33e8-746">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d33e8-747">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="d33e8-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="d33e8-748">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="d33e8-749">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="d33e8-750">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="d33e8-752">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="d33e8-752">Configure Windows Server</span></span>

1. <span data-ttu-id="d33e8-753">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="d33e8-754">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-755">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="d33e8-756">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="d33e8-757">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="d33e8-758">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="d33e8-759">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="d33e8-760">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine**>**Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="d33e8-761">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="d33e8-762">**.NET Core** &ndash; アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-762">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="d33e8-763">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="d33e8-764">**.NET Framework** &ndash; アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-764">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="d33e8-765">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-765">Install the required .NET Framework.</span></span> <span data-ttu-id="d33e8-766">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="d33e8-767">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="d33e8-768">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="d33e8-769">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="d33e8-770">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d33e8-771">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="d33e8-772">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d33e8-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="d33e8-773">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d33e8-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="d33e8-774">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="d33e8-775">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="d33e8-776">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d33e8-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="d33e8-777">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="d33e8-778">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="d33e8-779">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="d33e8-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="d33e8-780">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="d33e8-781">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="d33e8-782">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="d33e8-783">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="d33e8-784">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="d33e8-785">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="d33e8-786">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="d33e8-787">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="d33e8-788">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="d33e8-789">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="d33e8-790">`<URL>` &ndash; 完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="d33e8-790">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="d33e8-791">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-792">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="d33e8-793">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="d33e8-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="d33e8-794">`<USER>` &ndash; ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-794">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="d33e8-795">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="d33e8-796">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="d33e8-797">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="d33e8-798">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="d33e8-799">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="d33e8-800">`<IP>` &ndash; バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-800">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="d33e8-801">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="d33e8-802">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="d33e8-803">`<PORT>` &ndash; バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-803">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="d33e8-804">`<THUMBPRINT>` &ndash; X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-804">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="d33e8-805">`<GUID>` &ndash; 情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-805">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="d33e8-806">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="d33e8-807">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d33e8-807">In Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-808">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="d33e8-809">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="d33e8-810">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="d33e8-811">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="d33e8-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="d33e8-812">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-812">Open the app's project file.</span></span>
     * <span data-ttu-id="d33e8-813">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="d33e8-814">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-814">In the following example:</span></span>

   * <span data-ttu-id="d33e8-815">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="d33e8-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="d33e8-816">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="d33e8-817">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="d33e8-818">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="d33e8-819">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="d33e8-819">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="d33e8-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="d33e8-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="d33e8-821">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="d33e8-821">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="d33e8-822">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-822">Run the app.</span></span>

   <span data-ttu-id="d33e8-823">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d33e8-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="d33e8-824">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="d33e8-825">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="d33e8-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="d33e8-826">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="d33e8-827">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="d33e8-827">A development certificate is used in this example.</span></span> <span data-ttu-id="d33e8-828">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d33e8-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d33e8-830">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="d33e8-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d33e8-831">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d33e8-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="d33e8-832">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d33e8-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d33e8-833">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d33e8-833">Additional resources</span></span>

* [<span data-ttu-id="d33e8-834">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="d33e8-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="d33e8-835">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="d33e8-835">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="d33e8-836">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="d33e8-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="d33e8-837">ホスト</span><span class="sxs-lookup"><span data-stu-id="d33e8-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
