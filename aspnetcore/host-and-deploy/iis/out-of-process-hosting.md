---
title: IIS と ASP.NET Core を使用したアウトプロセス ホスティング
author: rick-anderson
description: IIS と ASP.NET Core モジュールを使用したアウトプロセス ホスティングについて説明します。
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060418"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="7e3f5-103">IIS と ASP.NET Core を使用したアウトプロセス ホスティング</span><span class="sxs-lookup"><span data-stu-id="7e3f5-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="7e3f5-104">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="7e3f5-105">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7e3f5-106">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7e3f5-107">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="7e3f5-109">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="7e3f5-110">ドライバーは、Web サイトの構成ポートで IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="7e3f5-111">構成されるポートは、通常 80 (HTTP) または 443 (HTTPS) です。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="7e3f5-112">モジュールでは、アプリのランダムなポートで Kestrel に要求を転送します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="7e3f5-113">ランダムなポートは 80 または 443 ではありません。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="7e3f5-114">ASP.NET Core モジュールは、起動時に環境変数によってポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="7e3f5-115"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 拡張機能は、`http://localhost:{PORT}` でリッスンするようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7e3f5-116">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7e3f5-117">モジュールは HTTPS 転送をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="7e3f5-118">要求は HTTPS を介して IIS によって受信された場合でも HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7e3f5-119">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインに転送されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7e3f5-120">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7e3f5-121">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7e3f5-122">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7e3f5-123">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7e3f5-124">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7e3f5-125">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="7e3f5-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7e3f5-126">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="7e3f5-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="7e3f5-127">`CreateHostBuilder` (`Program.cs`) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7e3f5-128">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="7e3f5-129">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="7e3f5-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="7e3f5-130">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7e3f5-131">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7e3f5-132">オプション</span><span class="sxs-lookup"><span data-stu-id="7e3f5-132">Option</span></span>                         | <span data-ttu-id="7e3f5-133">Default</span><span class="sxs-lookup"><span data-stu-id="7e3f5-133">Default</span></span> | <span data-ttu-id="7e3f5-134">設定</span><span class="sxs-lookup"><span data-stu-id="7e3f5-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7e3f5-135">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7e3f5-136">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7e3f5-137">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7e3f5-138">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7e3f5-139">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7e3f5-140">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7e3f5-141">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="7e3f5-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7e3f5-142">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)と ASP.NET Core モジュールは、次を転送するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="7e3f5-143">スキーム (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="7e3f5-144">要求元のリモート IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="7e3f5-145">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)は、Forwarded Headers Middleware を構成します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="7e3f5-146">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7e3f5-147">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7e3f5-148">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7e3f5-148">Out-of-process hosting model</span></span>

<span data-ttu-id="7e3f5-149">アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( `.csproj`) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="7e3f5-150">インプロセス ホスティングは `InProcess` で設定され、これが既定値です。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="7e3f5-151">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="7e3f5-152">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="7e3f5-153">アウトプロセスの場合は、[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> が呼び出され、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="7e3f5-154">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="7e3f5-155">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="7e3f5-156">プロセス名</span><span class="sxs-lookup"><span data-stu-id="7e3f5-156">Process name</span></span>

<span data-ttu-id="7e3f5-157">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="7e3f5-158">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="7e3f5-159">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="7e3f5-160">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="7e3f5-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="7e3f5-161">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="7e3f5-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="7e3f5-162">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="7e3f5-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="7e3f5-163">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="7e3f5-163">Forward Windows authentication tokens.</span></span>
