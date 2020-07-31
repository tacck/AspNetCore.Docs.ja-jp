---
title: ASP.NET Core で Windows 認証を構成する
author: scottaddie
description: IIS および HTTP.sys の ASP.NET Core で Windows 認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330686"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="08131-103">ASP.NET Core で Windows 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="08131-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="08131-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="08131-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08131-105">Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) は、 [IIS](xref:host-and-deploy/iis/index)、 [kestrel](xref:fundamentals/servers/kestrel)、 [HTTP.sys](xref:fundamentals/servers/httpsys)でホストされている ASP.NET Core アプリに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="08131-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08131-106">Windows 認証 (Negotiate、Kerberos、または NTLM 認証とも呼ばれます) は、 [IIS](xref:host-and-deploy/iis/index)または[HTTP.sys](xref:fundamentals/servers/httpsys)でホストされる ASP.NET Core アプリ用に構成できます。</span><span class="sxs-lookup"><span data-stu-id="08131-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="08131-107">Windows 認証では、オペレーティングシステムに依存して ASP.NET Core アプリのユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="08131-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="08131-108">サーバーが企業ネットワーク上で Active Directory ドメイン id または Windows アカウントを使用してユーザーを識別する場合は、Windows 認証を使用できます。</span><span class="sxs-lookup"><span data-stu-id="08131-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="08131-109">Windows 認証は、ユーザー、クライアントアプリ、および web サーバーが同じ Windows ドメインに属しているイントラネット環境に最適です。</span><span class="sxs-lookup"><span data-stu-id="08131-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="08131-110">Windows 認証は、HTTP/2 ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="08131-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="08131-111">認証チャレンジは HTTP/2 応答で送信できますが、認証する前にクライアントを HTTP/1.1 にダウングレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="08131-112">プロキシとロードバランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="08131-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="08131-113">Windows 認証は、主にイントラネットで使用されるステートフルなシナリオであり、プロキシまたはロードバランサーは通常、クライアントとサーバー間のトラフィックを処理しません。</span><span class="sxs-lookup"><span data-stu-id="08131-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="08131-114">プロキシまたはロードバランサーが使用されている場合、Windows 認証はプロキシまたはロードバランサーの場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="08131-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="08131-115">認証を処理します。</span><span class="sxs-lookup"><span data-stu-id="08131-115">Handles the authentication.</span></span>
* <span data-ttu-id="08131-116">認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="08131-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="08131-117">プロキシとロードバランサーを使用する環境での Windows 認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。</span><span class="sxs-lookup"><span data-stu-id="08131-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="08131-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="08131-118">IIS/IIS Express</span></span>

<span data-ttu-id="08131-119"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>で (名前空間) を呼び出して認証サービスを追加し <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="08131-120">起動設定 (デバッガー)</span><span class="sxs-lookup"><span data-stu-id="08131-120">Launch settings (debugger)</span></span>

<span data-ttu-id="08131-121">起動設定の構成は、IIS Express のファイルの*プロパティ/launchSettings.jsに*のみ影響し、Windows 認証用に IIS を構成することはありません。</span><span class="sxs-lookup"><span data-stu-id="08131-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="08131-122">サーバーの構成については、「 [IIS](#iis) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="08131-123">Visual Studio または .NET Core CLI で使用できる**Web アプリケーション**テンプレートは、Windows 認証をサポートするように構成できます。これにより、ファイルの*プロパティや launchSettings.js*が自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="08131-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08131-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08131-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08131-125">**新しいプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="08131-125">**New project**</span></span>

1. <span data-ttu-id="08131-126">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="08131-126">Create a new project.</span></span>
1. <span data-ttu-id="08131-127">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="08131-128">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-128">Select **Next**.</span></span>
1. <span data-ttu-id="08131-129">[**プロジェクト名**] フィールドに名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="08131-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="08131-130">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="08131-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="08131-131">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-131">Select **Create**.</span></span>
1. <span data-ttu-id="08131-132">[**認証**] で [**変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="08131-133">[**認証の変更**] ウィンドウで、[ **Windows 認証**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="08131-134">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-134">Select **OK**.</span></span>
1. <span data-ttu-id="08131-135">**[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="08131-136">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="08131-136">Select **Create**.</span></span>

<span data-ttu-id="08131-137">アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="08131-137">Run the app.</span></span> <span data-ttu-id="08131-138">ユーザー名は、表示されるアプリのユーザーインターフェイスに表示されます。</span><span class="sxs-lookup"><span data-stu-id="08131-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="08131-139">**既存のプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="08131-139">**Existing project**</span></span>

<span data-ttu-id="08131-140">プロジェクトのプロパティは、Windows 認証を有効にし、匿名認証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="08131-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="08131-141">**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="08131-142">**[デバッグ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="08131-143">[**匿名認証を有効にする**] のチェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="08131-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="08131-144">[ **Windows 認証を有効にする**] チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="08131-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="08131-145">プロパティページを保存して閉じます。</span><span class="sxs-lookup"><span data-stu-id="08131-145">Save and close the property page.</span></span>

<span data-ttu-id="08131-146">または、ファイルのlaunchSettings.jsのノードでプロパティを構成することもでき `iisSettings` ます。 *launchSettings.json*</span><span class="sxs-lookup"><span data-stu-id="08131-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="08131-147">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="08131-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08131-148">**新しいプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="08131-148">**New project**</span></span>

<span data-ttu-id="08131-149">引数 (ASP.NET Core Web App) を指定して[dotnet new](/dotnet/core/tools/dotnet-new)コマンドを実行 `webapp` し、スイッチを指定し `--auth Windows` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="08131-150">**既存のプロジェクト**</span><span class="sxs-lookup"><span data-stu-id="08131-150">**Existing project**</span></span>

<span data-ttu-id="08131-151">ファイルの `iisSettings` *launchSettings.js*のノードを更新します。</span><span class="sxs-lookup"><span data-stu-id="08131-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="08131-152">既存のプロジェクトを変更する場合は、プロジェクトファイルに[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)**また**は[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet パッケージのパッケージ参照が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="08131-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="08131-153">IIS</span><span class="sxs-lookup"><span data-stu-id="08131-153">IIS</span></span>

<span data-ttu-id="08131-154">IIS では、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して ASP.NET Core アプリをホストします。</span><span class="sxs-lookup"><span data-stu-id="08131-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="08131-155">Windows 認証は、 *web.config*ファイルを介して IIS 用に構成されます。</span><span class="sxs-lookup"><span data-stu-id="08131-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="08131-156">以下のセクションでは、その方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="08131-156">The following sections show how to:</span></span>

* <span data-ttu-id="08131-157">アプリの展開時にサーバーで Windows 認証をアクティブ化するローカル*web.config*ファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="08131-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="08131-158">サーバーに既に配置されている ASP.NET Core アプリの*web.config*ファイルを構成するには、IIS マネージャーを使用します。</span><span class="sxs-lookup"><span data-stu-id="08131-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="08131-159">まだインストールしていない場合は、IIS で ASP.NET Core アプリをホストできるようにします。</span><span class="sxs-lookup"><span data-stu-id="08131-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="08131-160">詳細については、「<xref:host-and-deploy/iis/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="08131-161">Windows 認証で IIS 役割サービスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="08131-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="08131-162">詳細については、「 [IIS 役割サービスで Windows 認証を有効にする (手順2参照)](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="08131-163">[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)は、既定で自動的に要求を認証するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="08131-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="08131-164">詳細については、「 [iis を使用した Windows でのホスト ASP.NET Core: iis オプション (自動認証)](xref:host-and-deploy/iis/index#iis-options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="08131-165">ASP.NET Core モジュールは、既定で Windows 認証トークンをアプリに転送するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="08131-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="08131-166">詳細については、「 [ASP.NET Core モジュール構成リファレンス: aspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="08131-167">次の**いずれか**の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="08131-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="08131-168">**プロジェクトを発行して配置する前に、** 次の*web.config*ファイルをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="08131-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="08131-169">プロジェクトが .NET Core SDK によって発行された場合 (プロジェクトファイルのプロパティがに設定されていない場合 `<IsTransformWebConfigDisabled>` `true` )、発行された*web.config*ファイルにはセクションが含まれ `<location><system.webServer><security><authentication>` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="08131-170">プロパティの詳細につい `<IsTransformWebConfigDisabled>` ては、「」を参照してください <xref:host-and-deploy/iis/index#webconfig-file> 。</span><span class="sxs-lookup"><span data-stu-id="08131-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="08131-171">**プロジェクトを発行および配置した後、** IIS マネージャーを使用してサーバー側の構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="08131-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="08131-172">IIS マネージャーで、[**接続**] サイドバーの [**サイト**] ノードの下の [iis] サイトを選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="08131-173">**IIS**領域で [**認証**] をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="08131-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="08131-174">[**匿名認証**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="08131-175">[**アクション**] サイドバーで [**無効**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="08131-176">**[Windows 認証]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="08131-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="08131-177">[**アクション**] サイドバーで [**有効化**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="08131-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="08131-178">これらの操作を実行すると、IIS マネージャーによって、アプリの*web.config*ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="08131-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="08131-179">`<system.webServer><security><authentication>`ノードは、およびの更新された設定を使用して追加され `anonymousAuthentication` `windowsAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="08131-180">`<system.webServer>`IIS マネージャーによって*web.config*ファイルに追加されたセクションは、 `<location>` アプリの発行時に .NET Core SDK によって追加されたアプリのセクションの外部にあります。</span><span class="sxs-lookup"><span data-stu-id="08131-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="08131-181">セクションはノードの外側に追加されるため `<location>` 、設定は[サブアプリ](xref:host-and-deploy/iis/index#sub-applications)によって現在のアプリに継承されます。</span><span class="sxs-lookup"><span data-stu-id="08131-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="08131-182">継承を防止するには、 `<security>` .NET Core SDK 提供されたセクション内に追加されたセクションを移動し `<location><system.webServer>` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="08131-183">Iis マネージャーを使用して IIS 構成を追加すると、サーバー上のアプリの*web.config*ファイルにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="08131-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="08131-184">サーバーの*web.config*のコピーがプロジェクトの*web.config*ファイルで置き換えられた場合、その後のアプリの展開では、サーバーの設定が上書きされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="08131-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="08131-185">設定を管理するには、次の**いずれか**の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="08131-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="08131-186">展開時にファイルが上書きされた後、IIS マネージャーを使用して、 *web.config*ファイルの設定をリセットします。</span><span class="sxs-lookup"><span data-stu-id="08131-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="08131-187">設定を使用して、アプリケーションに*web.config ファイル*をローカルに追加します。</span><span class="sxs-lookup"><span data-stu-id="08131-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="08131-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="08131-188">Kestrel</span></span>

<span data-ttu-id="08131-189">Windows、Linux、macOS で Negotiate と Kerberos を使用した Windows 認証をサポートするために、 [Kestrel](xref:fundamentals/servers/kestrel)と共に[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet パッケージを使用できます。</span><span class="sxs-lookup"><span data-stu-id="08131-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="08131-190">資格情報は、接続時に要求間で永続化できます。</span><span class="sxs-lookup"><span data-stu-id="08131-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="08131-191">*プロキシが Kestrel と1:1 の接続関係 (永続的な接続) を維持していない限り、プロキシでネゴシエート認証を使用することはできません。*</span><span class="sxs-lookup"><span data-stu-id="08131-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="08131-192">Negotiate ハンドラーは、基になるサーバーが Windows 認証をネイティブでサポートしているかどうかを検出します (有効になっている場合)。</span><span class="sxs-lookup"><span data-stu-id="08131-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="08131-193">サーバーが Windows 認証をサポートしていても無効になっている場合は、サーバーの実装を有効にするように求めるエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="08131-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="08131-194">サーバーで Windows 認証が有効になっている場合は、ネゴシエートハンドラーによって透過的に転送されます。</span><span class="sxs-lookup"><span data-stu-id="08131-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="08131-195">でおよびを呼び出して、認証サービスを追加 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> し <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="08131-196">でを呼び出して認証ミドルウェアを追加し <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="08131-197">ミドルウェアの詳細については、「」を参照してください <xref:fundamentals/middleware/index> 。</span><span class="sxs-lookup"><span data-stu-id="08131-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="08131-198">匿名要求が許可されます。</span><span class="sxs-lookup"><span data-stu-id="08131-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="08131-199">[ASP.NET Core 承認](xref:security/authorization/introduction)を使用して、認証のための匿名要求をチャレンジします。</span><span class="sxs-lookup"><span data-stu-id="08131-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="08131-200">Windows 環境構成</span><span class="sxs-lookup"><span data-stu-id="08131-200">Windows environment configuration</span></span>

<span data-ttu-id="08131-201">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)コンポーネントは、ユーザーモード認証を実行します。</span><span class="sxs-lookup"><span data-stu-id="08131-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="08131-202">サービスプリンシパル名 (Spn) は、コンピューターアカウントではなく、サービスを実行しているユーザーアカウントに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="08131-203">`setspn -S HTTP/myservername.mydomain.com myuser`管理コマンドシェルでを実行します。</span><span class="sxs-lookup"><span data-stu-id="08131-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="08131-204">Linux および macOS 環境構成</span><span class="sxs-lookup"><span data-stu-id="08131-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="08131-205">Linux または macOS コンピューターを Windows ドメインに参加させる手順については、「 [windows 認証を使用した SQL Server への Azure Data Studio の接続](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="08131-206">この手順では、ドメイン上の Linux マシン用のコンピューターアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="08131-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="08131-207">Spn をそのコンピューターアカウントに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="08131-208">「 [Windows 認証を使用して SQL Server に Azure Data Studio 接続する-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 」のガイダンスに従っている場合は、必要に応じ `python-software-properties` てをに置き換え `python3-software-properties` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="08131-209">Linux または macOS コンピューターがドメインに参加したら、次の手順に従って、[キータブファイル](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)に spn を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="08131-210">ドメインコントローラーで、新しい web サービス Spn をコンピューターアカウントに追加します。</span><span class="sxs-lookup"><span data-stu-id="08131-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="08131-211">[Ktpass](/windows-server/administration/windows-commands/ktpass)を使用して、キータブファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="08131-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="08131-212">一部のフィールドは、示されているとおりに大文字で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="08131-213">キータブファイルを Linux または macOS マシンにコピーします。</span><span class="sxs-lookup"><span data-stu-id="08131-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="08131-214">環境変数を使用して、キーボックスファイルを選択します。`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="08131-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="08131-215">`klist`を呼び出して、現在使用可能な spn を表示します。</span><span class="sxs-lookup"><span data-stu-id="08131-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="08131-216">キータブファイルにはドメインアクセス資格情報が含まれており、それに従って保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="08131-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="08131-217">HTTP.sys</span></span>

<span data-ttu-id="08131-218">[HTTP.sys](xref:fundamentals/servers/httpsys)は、ネゴシエート、NTLM、または基本認証を使用したカーネルモードの Windows 認証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="08131-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="08131-219"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>で (名前空間) を呼び出して認証サービスを追加し <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="08131-220">Windows 認証 (*Program.cs*) で HTTP.sys を使用するようにアプリの web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="08131-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="08131-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>は <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="08131-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="08131-222">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="08131-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="08131-223">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="08131-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="08131-224">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="08131-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="08131-225">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="08131-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="08131-226">HTTP.sys は、Nano Server バージョン1709以降ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="08131-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="08131-227">Nano Server で Windows 認証と HTTP.sys を使用するには、 [Server Core (microsoft/windowsservercore) コンテナー](https://hub.docker.com/r/microsoft/windowsservercore/)を使用します。</span><span class="sxs-lookup"><span data-stu-id="08131-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="08131-228">Server Core の詳細については、「 [Windows server の Server core インストールオプション](/windows-server/administration/server-core/what-is-server-core)について」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="08131-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="08131-229">ユーザーの承認</span><span class="sxs-lookup"><span data-stu-id="08131-229">Authorize users</span></span>

<span data-ttu-id="08131-230">匿名アクセスの構成の状態によって、 `[Authorize]` アプリで属性と属性がどのように使用されるかが決まり `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="08131-231">次の2つのセクションでは、匿名アクセスの許可されていない構成と許可される構成の状態の処理方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="08131-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="08131-232">匿名アクセスを許可しない</span><span class="sxs-lookup"><span data-stu-id="08131-232">Disallow anonymous access</span></span>

<span data-ttu-id="08131-233">Windows 認証が有効になっていて、匿名アクセスが無効になっている場合、 `[Authorize]` `[AllowAnonymous]` 属性と属性は影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="08131-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="08131-234">IIS サイトが匿名アクセスを許可しないように構成されている場合、要求はアプリに到達しません。</span><span class="sxs-lookup"><span data-stu-id="08131-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="08131-235">このため、属性は `[AllowAnonymous]` 適用されません。</span><span class="sxs-lookup"><span data-stu-id="08131-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="08131-236">匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="08131-236">Allow anonymous access</span></span>

<span data-ttu-id="08131-237">Windows 認証と匿名アクセスの両方が有効になっている場合は、 `[Authorize]` 属性と属性を使用し `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="08131-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="08131-238">`[Authorize]`属性を使用すると、認証を必要とするアプリのエンドポイントをセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="08131-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="08131-239">属性は、 `[AllowAnonymous]` `[Authorize]` 匿名アクセスを許可するアプリの属性よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="08131-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="08131-240">属性の使用の詳細については、「」を参照してください <xref:security/authorization/simple> 。</span><span class="sxs-lookup"><span data-stu-id="08131-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="08131-241">既定では、ページにアクセスする権限を持たないユーザーには、空の HTTP 403 応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="08131-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="08131-242">[Statuscodepages ページミドルウェア](xref:fundamentals/error-handling#usestatuscodepages)は、ユーザーが "アクセス拒否" のエクスペリエンスを向上させるように構成できます。</span><span class="sxs-lookup"><span data-stu-id="08131-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="08131-243">偽装</span><span class="sxs-lookup"><span data-stu-id="08131-243">Impersonation</span></span>

<span data-ttu-id="08131-244">ASP.NET Core が偽装を実装していません。</span><span class="sxs-lookup"><span data-stu-id="08131-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="08131-245">アプリは、アプリプールまたはプロセス id を使用して、すべての要求に対してアプリの id で実行されます。</span><span class="sxs-lookup"><span data-stu-id="08131-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="08131-246">アプリでユーザーに代わってアクションを実行する必要がある場合は、の[ターミナルインラインミドルウェア](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)で[WindowsIdentity](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)を使用します `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="08131-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="08131-247">このコンテキストで1つのアクションを実行し、コンテキストを閉じます。</span><span class="sxs-lookup"><span data-stu-id="08131-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="08131-248">`RunImpersonated`は非同期操作をサポートしていないため、複雑なシナリオでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="08131-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="08131-249">たとえば、要求またはミドルウェアチェーン全体のラップはサポートされていないか、推奨されません。</span><span class="sxs-lookup"><span data-stu-id="08131-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08131-250">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)パッケージは Windows、Linux、および macOS での認証を有効にしますが、偽装は windows でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="08131-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="08131-251">要求の変換</span><span class="sxs-lookup"><span data-stu-id="08131-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08131-252">IIS でホストする場合、は、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> ユーザーを初期化するために内部では呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="08131-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="08131-253">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="08131-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="08131-254">クレーム変換をアクティブにするコード例については、「」を参照してください <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。</span><span class="sxs-lookup"><span data-stu-id="08131-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08131-255">IIS インプロセスモードでホストする場合、は、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> ユーザーを初期化するために内部的に呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="08131-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="08131-256">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="08131-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="08131-257">インプロセスをホストするときに要求変換をアクティブにするコード例と詳細については、「」を参照してください <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。</span><span class="sxs-lookup"><span data-stu-id="08131-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="08131-258">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="08131-258">Additional resources</span></span>

* [<span data-ttu-id="08131-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="08131-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
