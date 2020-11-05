---
title: IIS と ASP.NET Core を使用したインプロセス ホスティング
author: rick-anderson
description: IIS と ASP.NET Core モジュールを使用したインプロセス ホスティングについて説明します。
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058481"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="5dab6-103">IIS と ASP.NET Core を使用したインプロセス ホスティング</span><span class="sxs-lookup"><span data-stu-id="5dab6-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="5dab6-104">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="5dab6-105">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="5dab6-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="5dab6-106">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="5dab6-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="5dab6-108">インプロセス ホスティングを有効にする</span><span class="sxs-lookup"><span data-stu-id="5dab6-108">Enable in-process hosting</span></span>

<span data-ttu-id="5dab6-109">ASP.NET Core 3.0 以降、IIS に展開されるすべてのアプリに対してインプロセス ホスティングが既定では有効になっています。</span><span class="sxs-lookup"><span data-stu-id="5dab6-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="5dab6-110">インプロセス ホスティング用にアプリを明示的に構成するには、プロジェクト ファイル (`.csproj`) で、`<AspNetCoreHostingModel>` プロパティの値を `InProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="5dab6-111">全般的なアーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="5dab6-111">General architecture</span></span>

<span data-ttu-id="5dab6-112">要求の一般的なフローは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5dab6-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="5dab6-113">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="5dab6-114">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="5dab6-115">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="5dab6-116">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="5dab6-117">IIS HTTP サーバーによって要求が処理された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5dab6-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="5dab6-118">要求は ASP.NET Core ミドルウェア パイプラインに送信されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="5dab6-119">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="5dab6-120">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="5dab6-121">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="5dab6-122">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (`w3wp.exe` または `iisexpress.exe`) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="5dab6-123">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) へのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="5dab6-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="5dab6-124">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="5dab6-125">アプリケーションの構成</span><span class="sxs-lookup"><span data-stu-id="5dab6-125">Application configuration</span></span>

<span data-ttu-id="5dab6-126">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="5dab6-127">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="5dab6-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="5dab6-128">オプション</span><span class="sxs-lookup"><span data-stu-id="5dab6-128">Option</span></span> | <span data-ttu-id="5dab6-129">Default</span><span class="sxs-lookup"><span data-stu-id="5dab6-129">Default</span></span> | <span data-ttu-id="5dab6-130">設定</span><span class="sxs-lookup"><span data-stu-id="5dab6-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="5dab6-131">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="5dab6-132">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="5dab6-133">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dab6-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="5dab6-134">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dab6-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="5dab6-135">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="5dab6-136">`HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるか。</span><span class="sxs-lookup"><span data-stu-id="5dab6-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="5dab6-137">`HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="5dab6-138">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5dab6-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="5dab6-139">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="5dab6-140">`maxAllowedContentLength`を引き上げるには、`web.config` 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="5dab6-141">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dab6-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="5dab6-142">インプロセスおよびアウトプロセス ホスティングの相違点</span><span class="sxs-lookup"><span data-stu-id="5dab6-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="5dab6-143">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5dab6-144">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5dab6-145">インプロセスの場合は、[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> が呼び出され、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="5dab6-146">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5dab6-147">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5dab6-148">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5dab6-149">[`requestTimeout` 属性](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5dab6-150">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5dab6-151">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-151">Use one app pool per app.</span></span>

* <span data-ttu-id="5dab6-152">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dab6-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="5dab6-153">たとえば、32 ビット (x86) 用に公開されたアプリの場合、IIS アプリケーション プールで 32 ビットが有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dab6-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="5dab6-154">詳細については、「[IIS サイトを作成する](xref:tutorials/publish-to-iis#create-the-iis-site)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dab6-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="5dab6-155">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-155">Client disconnects are detected.</span></span> <span data-ttu-id="5dab6-156">クライアントが切断されると、[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="5dab6-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5dab6-157">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5dab6-158">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5dab6-159"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="5dab6-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="5dab6-160">[Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="5dab6-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
