---
title: ASP.NET Core Blazor サーバーのホストとデプロイ
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー アプリをホストおよびデプロイする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: c02d005aa5e37bc359e1c104a19c387f1c16fa34
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243552"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="92c39-103">Blazor サーバーをホストおよびデプロイする</span><span class="sxs-lookup"><span data-stu-id="92c39-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="92c39-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="92c39-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="92c39-105">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="92c39-105">Host configuration values</span></span>

<span data-ttu-id="92c39-106">[Blazor サーバー アプリ](xref:blazor/hosting-models#blazor-server)では、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="92c39-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="92c39-107">配置</span><span class="sxs-lookup"><span data-stu-id="92c39-107">Deployment</span></span>

<span data-ttu-id="92c39-108">[Blazor サーバーのホスティング モデル](xref:blazor/hosting-models#blazor-server)を使用する場合、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="92c39-109">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="92c39-110">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="92c39-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="92c39-111">Visual Studio には **Blazor サーバー アプリ** プロジェクト テンプレートが含まれています ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。</span><span class="sxs-lookup"><span data-stu-id="92c39-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="92c39-112">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="92c39-112">Scalability</span></span>

<span data-ttu-id="92c39-113">Blazor サーバー アプリで使用できるインフラストラクチャを最大限に活用できるようにデプロイを計画します。</span><span class="sxs-lookup"><span data-stu-id="92c39-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="92c39-114">Blazor サーバー アプリのスケーラビリティに対処するには、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="92c39-115">[Blazor サーバー アプリの基礎](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="92c39-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="92c39-116">展開サーバー</span><span class="sxs-lookup"><span data-stu-id="92c39-116">Deployment server</span></span>

<span data-ttu-id="92c39-117">1 台のサーバーのスケーラビリティ (スケールアップ) を検討する場合、アプリに使用できるメモリは、ユーザーの要求が増えたときにアプリによって使い果たされる最初のリソースになります。</span><span class="sxs-lookup"><span data-stu-id="92c39-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="92c39-118">サーバー上の使用可能なメモリは、以下の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="92c39-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="92c39-119">サーバーがサポートできるアクティブ回線の数。</span><span class="sxs-lookup"><span data-stu-id="92c39-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="92c39-120">クライアントでの UI の待機時間。</span><span class="sxs-lookup"><span data-stu-id="92c39-120">UI latency on the client.</span></span>

<span data-ttu-id="92c39-121">セキュリティで保護されたスケーラブルな Blazor サーバー アプリを構築するためのガイダンスについては、「<xref:blazor/security/server/threat-mitigation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="92c39-122">各回線では、最小限の *Hello World* スタイルのアプリに約 250 KB のメモリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="92c39-123">回線のサイズは、アプリのコードと各コンポーネントに関連付けられている状態の保守要件によって変わります。</span><span class="sxs-lookup"><span data-stu-id="92c39-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="92c39-124">アプリとインフラストラクチャの開発時にはリソースのニーズを測定することをお勧めしますが、展開ターゲットを計画する際に、次のベースラインを出発点にすることができます。アプリで 5,000 人の同時ユーザーをサポートすることを想定している場合は、アプリに対して少なくとも 1.3 GB のサーバー メモリ (またはユーザーあたり最大 273 KB) の予算を割り当てること検討してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="92c39-125"> 構成</span><span class="sxs-lookup"><span data-stu-id="92c39-125"> configuration</span></span>

Blazor<span data-ttu-id="92c39-126"> サーバー アプリでは、ブラウザーとの通信に ASP.NET Core SignalR が使用されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="92c39-127">[SignalR のホストとスケーリングの条件](xref:signalr/publish-to-azure-web-app)は、Blazor サーバー アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="92c39-128"> は、待ち時間、信頼性、および[セキュリティ](xref:signalr/security)が低いために WebSocket を SignalR トランスポートとして使用する場合に最適です。</span><span class="sxs-lookup"><span data-stu-id="92c39-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="92c39-129">WebSocket が使用できない場合や、ロング ポーリングを使用するようにアプリが明示的に構成されている場合は、SignalR によってロング ポーリングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="92c39-130">Azure App Service にデプロイする場合は、サービスの Azure portal 設定で WebSocket を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="92c39-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="92c39-131">Azure App Service 用にアプリを構成する方法の詳細については、[SignalR の発行ガイドライン](xref:signalr/publish-to-azure-web-app)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="92c39-132">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="92c39-132">Azure SignalR Service</span></span>

<span data-ttu-id="92c39-133">Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="92c39-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="92c39-134">このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="92c39-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="92c39-135">さらに、SignalR サービスのグローバル リーチとハイパフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="92c39-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="92c39-136">アプリの構成 (および必要に応じてプロビジョニング) を行うために、Azure SignalR Service によって次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="92c39-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="92c39-137">サービスで "*スティッキー セッション*" をサポートできるようにします。それにより、クライアントは[事前レンダリングのときに同じサーバーにリダイレクトされます](xref:blazor/hosting-models#connection-to-the-server)。</span><span class="sxs-lookup"><span data-stu-id="92c39-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="92c39-138">`ServerStickyMode` オプションまたは構成値を `Required` に設定します。</span><span class="sxs-lookup"><span data-stu-id="92c39-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="92c39-139">通常、アプリでは次の方法の**いずれか 1 つ**を使用して構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="92c39-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="92c39-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="92c39-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="92c39-141">構成 (次の方法の**いずれか**を使用):</span><span class="sxs-lookup"><span data-stu-id="92c39-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="92c39-142">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="92c39-142">`appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="92c39-143">Azure portal で App Service の **[構成]**  >  **[アプリケーションの設定]** (**名前**: `Azure:SignalR:ServerStickyMode`、**値**: `Required`)。</span><span class="sxs-lookup"><span data-stu-id="92c39-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="92c39-144">Blazor サーバー アプリ用に、Visual Studio に Azure アプリ発行プロファイルを作成する。</span><span class="sxs-lookup"><span data-stu-id="92c39-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="92c39-145">プロファイルに **Azure SignalR Service** の依存関係を追加する。</span><span class="sxs-lookup"><span data-stu-id="92c39-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="92c39-146">Azure サブスクリプションに、アプリに割り当てる既存の Azure SignalR Service のインスタンスがない場合は、 **[新しい Azure SignalR Service のインスタンスを作成する]** を選択して新しいサービス インスタンスをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="92c39-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="92c39-147">Azure にアプリを公開します。</span><span class="sxs-lookup"><span data-stu-id="92c39-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="92c39-148">IIS</span><span class="sxs-lookup"><span data-stu-id="92c39-148">IIS</span></span>

<span data-ttu-id="92c39-149">IIS を使用する場合は、次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="92c39-149">When using IIS, enable:</span></span>

* <span data-ttu-id="92c39-150">[IIS での WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="92c39-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="92c39-151">[アプリケーション要求ルーティング処理でのスティッキー セッション](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="92c39-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="92c39-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="92c39-152">Kubernetes</span></span>

<span data-ttu-id="92c39-153">次の[スティッキー セッションに対する Kubernetes 注釈](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)を使用して、イングレス定義を作成します。</span><span class="sxs-lookup"><span data-stu-id="92c39-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="92c39-154">Nginx を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="92c39-154">Linux with Nginx</span></span>

<span data-ttu-id="92c39-155">SignalR WebSocket が正常に機能するには、プロキシの `Upgrade` と `Connection` のヘッダーが次の値に設定されていること、および `$connection_upgrade` が次のどちらかにマップされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="92c39-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="92c39-156">アップグレード ヘッダーの値 (既定)。</span><span class="sxs-lookup"><span data-stu-id="92c39-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="92c39-157">アップグレード ヘッダーが存在しないか、空である場合は `close`。</span><span class="sxs-lookup"><span data-stu-id="92c39-157">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="92c39-158">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="92c39-159">WebSocket プロキシとしての NGINX</span><span class="sxs-lookup"><span data-stu-id="92c39-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="92c39-160">WebSocket プロキシ</span><span class="sxs-lookup"><span data-stu-id="92c39-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="92c39-161">Apache を使用した Linux</span><span class="sxs-lookup"><span data-stu-id="92c39-161">Linux with Apache</span></span>

<span data-ttu-id="92c39-162">Linux 上の Apache の背後に Blazor アプリをホストするには、HTTP および Websocket トラフィック用に `ProxyPass` を構成します。</span><span class="sxs-lookup"><span data-stu-id="92c39-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="92c39-163">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="92c39-163">In the following example:</span></span>

* <span data-ttu-id="92c39-164">Kestrel サーバーは、ホスト コンピューター上で実行されています。</span><span class="sxs-lookup"><span data-stu-id="92c39-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="92c39-165">このアプリは、ポート 5000 でトラフィックをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="92c39-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="92c39-166">次のモジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="92c39-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="92c39-167">WebSockets エラーをブラウザ コンソールで確認します。</span><span class="sxs-lookup"><span data-stu-id="92c39-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="92c39-168">エラーの例:</span><span class="sxs-lookup"><span data-stu-id="92c39-168">Example errors:</span></span>

* <span data-ttu-id="92c39-169">Firefox は ws://the-domain-name.tld/_blazor?id=XXX のサーバーに接続を確立できません。</span><span class="sxs-lookup"><span data-stu-id="92c39-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="92c39-170">エラー :'WebSockets' の転送の開始に失敗しました。エラー :転送のエラーがありました。</span><span class="sxs-lookup"><span data-stu-id="92c39-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="92c39-171">エラー :'LongPolling' の転送の開始に失敗しました。TypeError: this.transport は定義されていません</span><span class="sxs-lookup"><span data-stu-id="92c39-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="92c39-172">エラー :利用可能ないかなる転送を使用しても、サーバーに接続できませんでした。</span><span class="sxs-lookup"><span data-stu-id="92c39-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="92c39-173">WebSockets が失敗しました</span><span class="sxs-lookup"><span data-stu-id="92c39-173">WebSockets failed</span></span>
* <span data-ttu-id="92c39-174">エラー :接続が 'Connected' 状態ではない場合、データは送信できません。</span><span class="sxs-lookup"><span data-stu-id="92c39-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="92c39-175">詳細については、[Apache のドキュメント](https://httpd.apache.org/docs/current/mod/mod_proxy.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92c39-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="92c39-176">ネットワーク待機時間の測定</span><span class="sxs-lookup"><span data-stu-id="92c39-176">Measure network latency</span></span>

<span data-ttu-id="92c39-177">次の例に示すように、[JS 相互運用機能](xref:blazor/call-javascript-from-dotnet)を使用してネットワーク待機時間を測定できます。</span><span class="sxs-lookup"><span data-stu-id="92c39-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="92c39-178">妥当な UI エクスペリエンスを実現するために、UI の待機時間を 250 ミリ秒以下にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="92c39-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
