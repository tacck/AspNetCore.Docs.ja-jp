---
title: ASP.NET Core のホストと展開
author: rick-anderson
description: ホスティング環境を設定し、ASP.NET Core アプリを展開する方法を学習します。
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252969"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="f894d-103">ASP.NET Core のホストと展開</span><span class="sxs-lookup"><span data-stu-id="f894d-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f894d-104">通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="f894d-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="f894d-105">ホスティング サーバー上のフォルダーに発行されたアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="f894d-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="f894d-106">要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。</span><span class="sxs-lookup"><span data-stu-id="f894d-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="f894d-107">リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。</span><span class="sxs-lookup"><span data-stu-id="f894d-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="f894d-108">フォルダーに発行する</span><span class="sxs-lookup"><span data-stu-id="f894d-108">Publish to a folder</span></span>

<span data-ttu-id="f894d-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="f894d-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="f894d-110">Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="f894d-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="f894d-111">設定ファイルを発行する</span><span class="sxs-lookup"><span data-stu-id="f894d-111">Publish settings files</span></span>

<span data-ttu-id="f894d-112">`*.json` ファイルは既定で発行されます。</span><span class="sxs-lookup"><span data-stu-id="f894d-112">`*.json` files are published by default.</span></span> <span data-ttu-id="f894d-113">他の設定ファイルを発行するには、プロジェクト ファイルの [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) 要素でそれらを指定します。</span><span class="sxs-lookup"><span data-stu-id="f894d-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="f894d-114">次の例は、XML ファイルを発行します。</span><span class="sxs-lookup"><span data-stu-id="f894d-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="f894d-115">フォルダーの内容</span><span class="sxs-lookup"><span data-stu-id="f894d-115">Folder contents</span></span>

<span data-ttu-id="f894d-116">*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="f894d-117">.NET Core アプリは、*自己完結型展開* または *フレームワーク依存展開* として発行できます。</span><span class="sxs-lookup"><span data-stu-id="f894d-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="f894d-118">アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="f894d-119">アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。</span><span class="sxs-lookup"><span data-stu-id="f894d-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="f894d-120">既定の展開モデルはフレームワークに依存します。</span><span class="sxs-lookup"><span data-stu-id="f894d-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="f894d-121">詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="f894d-122">*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="f894d-123">詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="f894d-124">プロセス マネージャーを設定する</span><span class="sxs-lookup"><span data-stu-id="f894d-124">Set up a process manager</span></span>

<span data-ttu-id="f894d-125">ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="f894d-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="f894d-126">起動と再起動を自動化するには、プロセス マネージャーが必要です。</span><span class="sxs-lookup"><span data-stu-id="f894d-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="f894d-127">ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f894d-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="f894d-128">Linux</span><span class="sxs-lookup"><span data-stu-id="f894d-128">Linux</span></span>
  * [<span data-ttu-id="f894d-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="f894d-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f894d-130">Apache</span><span class="sxs-lookup"><span data-stu-id="f894d-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="f894d-131">Windows</span><span class="sxs-lookup"><span data-stu-id="f894d-131">Windows</span></span>
  * [<span data-ttu-id="f894d-132">IIS</span><span class="sxs-lookup"><span data-stu-id="f894d-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f894d-133">Windows サービス</span><span class="sxs-lookup"><span data-stu-id="f894d-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="f894d-134">リバース プロキシを設定する</span><span class="sxs-lookup"><span data-stu-id="f894d-134">Set up a reverse proxy</span></span>

<span data-ttu-id="f894d-135">アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f894d-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="f894d-136">リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="f894d-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="f894d-137">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f894d-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="f894d-138">詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="f894d-139">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f894d-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="f894d-140">詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f894d-141">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="f894d-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f894d-142">プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="f894d-143">追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="f894d-144">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="f894d-145">Visual Studio と MSBuild を使用してデプロイを自動化する</span><span class="sxs-lookup"><span data-stu-id="f894d-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="f894d-146">多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。</span><span class="sxs-lookup"><span data-stu-id="f894d-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="f894d-147">たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="f894d-148">Visual Studio では Web 展開用に [MSBuild](/visualstudio/msbuild/msbuild) が使用されます。MSBuild は、展開時に他の多くの作業を行うようにカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="f894d-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="f894d-149">詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="f894d-150">[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。</span><span class="sxs-lookup"><span data-stu-id="f894d-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="f894d-151">Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f894d-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="f894d-152">詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="f894d-153">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="f894d-153">Publish to Azure</span></span>

<span data-ttu-id="f894d-154">Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f894d-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="f894d-155">その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="f894d-156">Windows での MSDeploy を使用した発行</span><span class="sxs-lookup"><span data-stu-id="f894d-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="f894d-157">Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f894d-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="f894d-158">インターネット インフォメーション サービス (IIS)</span><span class="sxs-lookup"><span data-stu-id="f894d-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="f894d-159">*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="f894d-160">Web ファームでのホスト</span><span class="sxs-lookup"><span data-stu-id="f894d-160">Host in a web farm</span></span>

<span data-ttu-id="f894d-161">Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="f894d-162">Docker でのホスト</span><span class="sxs-lookup"><span data-stu-id="f894d-162">Host on Docker</span></span>

<span data-ttu-id="f894d-163">詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="f894d-164">正常性チェックを実行する</span><span class="sxs-lookup"><span data-stu-id="f894d-164">Perform health checks</span></span>

<span data-ttu-id="f894d-165">アプリとその依存関係に対して正常性チェックを実行するには、正常性チェックのミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f894d-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="f894d-166">詳細については、「<xref:host-and-deploy/health-checks>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f894d-167">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f894d-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="f894d-168">ASP.NET ホスティング</span><span class="sxs-lookup"><span data-stu-id="f894d-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f894d-169">通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="f894d-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="f894d-170">ホスティング サーバー上のフォルダーに発行されたアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="f894d-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="f894d-171">要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。</span><span class="sxs-lookup"><span data-stu-id="f894d-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="f894d-172">リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。</span><span class="sxs-lookup"><span data-stu-id="f894d-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="f894d-173">フォルダーに発行する</span><span class="sxs-lookup"><span data-stu-id="f894d-173">Publish to a folder</span></span>

<span data-ttu-id="f894d-174">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="f894d-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="f894d-175">Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="f894d-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="f894d-176">フォルダーの内容</span><span class="sxs-lookup"><span data-stu-id="f894d-176">Folder contents</span></span>

<span data-ttu-id="f894d-177">*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="f894d-178">.NET Core アプリは、*自己完結型展開* または *フレームワーク依存展開* として発行できます。</span><span class="sxs-lookup"><span data-stu-id="f894d-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="f894d-179">アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="f894d-180">アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。</span><span class="sxs-lookup"><span data-stu-id="f894d-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="f894d-181">既定の展開モデルはフレームワークに依存します。</span><span class="sxs-lookup"><span data-stu-id="f894d-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="f894d-182">詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="f894d-183">*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f894d-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="f894d-184">詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="f894d-185">プロセス マネージャーを設定する</span><span class="sxs-lookup"><span data-stu-id="f894d-185">Set up a process manager</span></span>

<span data-ttu-id="f894d-186">ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="f894d-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="f894d-187">起動と再起動を自動化するには、プロセス マネージャーが必要です。</span><span class="sxs-lookup"><span data-stu-id="f894d-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="f894d-188">ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f894d-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="f894d-189">Linux</span><span class="sxs-lookup"><span data-stu-id="f894d-189">Linux</span></span>
  * [<span data-ttu-id="f894d-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="f894d-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="f894d-191">Apache</span><span class="sxs-lookup"><span data-stu-id="f894d-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="f894d-192">Windows</span><span class="sxs-lookup"><span data-stu-id="f894d-192">Windows</span></span>
  * [<span data-ttu-id="f894d-193">IIS</span><span class="sxs-lookup"><span data-stu-id="f894d-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="f894d-194">Windows サービス</span><span class="sxs-lookup"><span data-stu-id="f894d-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="f894d-195">リバース プロキシを設定する</span><span class="sxs-lookup"><span data-stu-id="f894d-195">Set up a reverse proxy</span></span>

<span data-ttu-id="f894d-196">アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f894d-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="f894d-197">リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="f894d-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="f894d-198">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f894d-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="f894d-199">詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f894d-200">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="f894d-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f894d-201">プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="f894d-202">追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="f894d-203">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="f894d-204">Visual Studio と MSBuild を使用してデプロイを自動化する</span><span class="sxs-lookup"><span data-stu-id="f894d-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="f894d-205">多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。</span><span class="sxs-lookup"><span data-stu-id="f894d-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="f894d-206">たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。</span><span class="sxs-lookup"><span data-stu-id="f894d-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="f894d-207">Visual Studio では Web 展開で MSBuild を使用します。この MSBuild は、展開時に他の多くの作業を行うためにカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="f894d-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="f894d-208">詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="f894d-209">[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。</span><span class="sxs-lookup"><span data-stu-id="f894d-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="f894d-210">Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f894d-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="f894d-211">詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="f894d-212">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="f894d-212">Publish to Azure</span></span>

<span data-ttu-id="f894d-213">Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f894d-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="f894d-214">その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="f894d-215">Windows での MSDeploy を使用した発行</span><span class="sxs-lookup"><span data-stu-id="f894d-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="f894d-216">Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f894d-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="f894d-217">インターネット インフォメーション サービス (IIS)</span><span class="sxs-lookup"><span data-stu-id="f894d-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="f894d-218">*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="f894d-219">Web ファームでのホスト</span><span class="sxs-lookup"><span data-stu-id="f894d-219">Host in a web farm</span></span>

<span data-ttu-id="f894d-220">Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="f894d-221">Docker でのホスト</span><span class="sxs-lookup"><span data-stu-id="f894d-221">Host on Docker</span></span>

<span data-ttu-id="f894d-222">詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f894d-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f894d-223">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f894d-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="f894d-224">ASP.NET ホスティング</span><span class="sxs-lookup"><span data-stu-id="f894d-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
