---
title: 詳細な構成
author: rick-anderson
description: ASP.NET Core モジュールとインターネット インフォメーション サービス (IIS) に関する詳細な構成。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058614"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="d13d1-103">ASP.NET Core モジュールと IIS の詳細な構成</span><span class="sxs-lookup"><span data-stu-id="d13d1-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="d13d1-104">この記事では、ASP.NET Core モジュールと IIS の詳細な構成のオプションとシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="d13d1-105">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="d13d1-105">Modify the stack size</span></span>

<span data-ttu-id="d13d1-106">" *インプロセス ホスティング モデルを使用している場合にのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d13d1-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="d13d1-107">マネージド スタックのサイズは、`web.config` ファイルで `stackSize` の設定を使用して構成します (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="d13d1-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="d13d1-108">既定のサイズは 1,048,576 バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="d13d1-109">次の例では、スタック サイズを 2 MB (2,097,152 バイト) に変更します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="d13d1-110">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="d13d1-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="d13d1-111">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d13d1-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="d13d1-112">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="d13d1-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="d13d1-113">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="d13d1-114">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="d13d1-115">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="d13d1-116">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="d13d1-117">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="d13d1-118">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="d13d1-119">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="d13d1-120">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="d13d1-121">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="d13d1-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="d13d1-122">ASP.NET Core モジュールのインストーラーは、`TrustedInstaller` アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="d13d1-123">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の `applicationHost.config` ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="d13d1-124">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="d13d1-125">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="d13d1-126">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="d13d1-127">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-127">Run the installer.</span></span>
1. <span data-ttu-id="d13d1-128">更新された `applicationHost.config` ファイルをファイル共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="d13d1-129">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="d13d1-130">データの保護</span><span class="sxs-lookup"><span data-stu-id="d13d1-130">Data protection</span></span>

<span data-ttu-id="d13d1-131">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="d13d1-132">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="d13d1-133">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="d13d1-134">データ保護キー リングがメモリに格納されている場合、アプリを再起動すると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="d13d1-135">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="d13d1-136">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="d13d1-137">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="d13d1-138">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="d13d1-139">キーリングを保持するために IIS でのデータ保護を構成するには、次の **いずれか** の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="d13d1-140">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="d13d1-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="d13d1-141">ASP.NET Core アプリで使用されるデータ保護キーは、アプリの外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="d13d1-142">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="d13d1-143">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="d13d1-144">このスクリプトを使用すると、HKLM レジストリに、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできるレジストリ キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="d13d1-145">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="d13d1-146">Web ファームのシナリオの場合は、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="d13d1-147">既定では、キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="d13d1-148">ネットワーク共有に対するファイルのアクセス許可が、アプリが実行される Windows アカウントに確実に限定されているようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="d13d1-149">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="d13d1-150">ユーザーが証明書をアップロードできるメカニズムを検討します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="d13d1-151">ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="d13d1-152">詳細については、「<xref:security/data-protection/configuration/overview>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="d13d1-153">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="d13d1-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="d13d1-154">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="d13d1-155">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="d13d1-156">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="d13d1-157">キーは `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="d13d1-158">アプリ プールの [`setProfileEnvironment` 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="d13d1-159">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="d13d1-160">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="d13d1-161">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="d13d1-162">`%windir%/system32/inetsrv/config` フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="d13d1-163">`applicationHost.config` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="d13d1-164">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="d13d1-165">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="d13d1-166">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="d13d1-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="d13d1-167">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="d13d1-168">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="d13d1-169">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="d13d1-170">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="d13d1-171">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="d13d1-172">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="d13d1-173">[コードでデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="d13d1-174">**データ保護に対してコンピューター全体のポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="d13d1-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="d13d1-175">データ保護システムによる、Data Protection API を使用するすべてのアプリに対する、既定で[コンピューター全体に適用されるポリシー](xref:security/data-protection/configuration/machine-wide-policy)の設定のサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="d13d1-176">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="d13d1-177">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="d13d1-177">IIS configuration</span></span>

<span data-ttu-id="d13d1-178">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d13d1-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="d13d1-179">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="d13d1-180">**[管理]** メニューから **役割と機能の追加** ウィザードを使用するか、 **サーバー マネージャー** にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="d13d1-181">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="d13d1-183">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="d13d1-184">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="d13d1-186">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d13d1-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d13d1-187">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="d13d1-188">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d13d1-189">詳細については、「[Windows 認証 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d13d1-190">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d13d1-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d13d1-191">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d13d1-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d13d1-192">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="d13d1-193">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d13d1-194">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d13d1-195">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="d13d1-196">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="d13d1-197">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="d13d1-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="d13d1-198">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d13d1-199">**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]**  >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="d13d1-200">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="d13d1-201">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="d13d1-202">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="d13d1-203">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="d13d1-204">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="d13d1-205">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="d13d1-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="d13d1-206">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="d13d1-207">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="d13d1-208">詳細については、「[Windows 認証 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="d13d1-209">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="d13d1-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="d13d1-210">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d13d1-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="d13d1-211">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d13d1-212">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="d13d1-213">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="d13d1-214">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-214">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="d13d1-216">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="d13d1-216">Virtual Directories</span></span>

<span data-ttu-id="d13d1-217">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="d13d1-218">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="d13d1-219">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="d13d1-219">Sub-applications</span></span>

<span data-ttu-id="d13d1-220">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="d13d1-221">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="d13d1-222">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="d13d1-223">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="d13d1-224">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="d13d1-225">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="d13d1-226">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="d13d1-227">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="d13d1-228">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="d13d1-229">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="d13d1-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="d13d1-230">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="d13d1-231">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="d13d1-232">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="d13d1-233">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="d13d1-234">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="d13d1-235">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-235">Select **OK**.</span></span>

<span data-ttu-id="d13d1-236">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="d13d1-237">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="d13d1-238">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="d13d1-238">Application Pools</span></span>

<span data-ttu-id="d13d1-239">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="d13d1-240">インプロセス ホスティング: 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="d13d1-241">アウトプロセス ホスティング: アプリケーションをそれぞれ専用のアプリケーション プールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="d13d1-242">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="d13d1-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="d13d1-243">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="d13d1-244">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="d13d1-245">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="d13d1-245">Application Pool Identity</span></span>

<span data-ttu-id="d13d1-246">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="d13d1-247">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="d13d1-248">IIS 管理コンソールの、アプリ プールに対する **[詳細設定]** で、確実に **Identity** で `ApplicationPoolIdentity` が使用されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="d13d1-250">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="d13d1-251">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="d13d1-252">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="d13d1-253">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="d13d1-254">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="d13d1-255">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="d13d1-256">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="d13d1-257">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="d13d1-258">**[選択するオブジェクト名を入力してください]** 領域に、`IIS AppPool\{APP POOL NAME}` という形式で入力します。プレースホルダー `{APP POOL NAME}` はアプリ プール名です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="d13d1-259">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-259">Select the **Check Names** button.</span></span> <span data-ttu-id="d13d1-260">*DefaultAppPool* で、`IIS AppPool\DefaultAppPool` を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="d13d1-261">**[名前の確認]** ボタンが選択されているときには、`DefaultAppPool` の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="d13d1-262">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="d13d1-263">`IIS AppPool\{APP POOL NAME}` 形式を使用します。ここで、プレースホルダー `{APP POOL NAME}` は、オブジェクト名を確認するときのアプリ プール名です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="d13d1-265">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-265">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="d13d1-267">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="d13d1-268">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="d13d1-269">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="d13d1-270">たとえば、 *DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="d13d1-271">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="d13d1-272">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d13d1-272">HTTP/2 support</span></span>

<span data-ttu-id="d13d1-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d13d1-274">インプロセス</span><span class="sxs-lookup"><span data-stu-id="d13d1-274">In-process</span></span>
  * <span data-ttu-id="d13d1-275">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d13d1-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d13d1-276">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d13d1-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d13d1-277">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="d13d1-277">Out-of-process</span></span>
  * <span data-ttu-id="d13d1-278">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="d13d1-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="d13d1-279">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="d13d1-280">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d13d1-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d13d1-281">インプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d13d1-282">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d13d1-283">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d13d1-284">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="d13d1-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d13d1-285">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d13d1-286">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="d13d1-287">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="d13d1-287">CORS preflight requests</span></span>

<span data-ttu-id="d13d1-288">" *このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d13d1-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="d13d1-289">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="d13d1-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="d13d1-290">OPTIONS 要求を渡すように `web.config` でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d13d1-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="d13d1-291">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d13d1-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="d13d1-292">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="d13d1-293">[Application Initialization モジュール](#application-initialization-module): アプリのホストされている[インプロセス](xref:host-and-deploy/iis/in-process-hosting)または[アウトプロセス](xref:host-and-deploy/iis/out-of-process-hosting)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="d13d1-294">[アイドル タイムアウト](#idle-timeout): アプリのホストされている[インプロセス](xref:host-and-deploy/iis/in-process-hosting)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="d13d1-295">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="d13d1-295">Application Initialization Module</span></span>

<span data-ttu-id="d13d1-296">" *アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d13d1-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="d13d1-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="d13d1-298">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-298">The request triggers the app to start.</span></span> <span data-ttu-id="d13d1-299">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="d13d1-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="d13d1-300">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="d13d1-301">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="d13d1-302">**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]**  >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="d13d1-303">**[インターネット インフォメーション サービス]**  >  **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="d13d1-304">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d13d1-305">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="d13d1-306">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="d13d1-307">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="d13d1-308">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="d13d1-309">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="d13d1-310">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="d13d1-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="d13d1-311">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d13d1-312">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d13d1-313">既定の **[開始モード]** は `OnDemand` です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="d13d1-314">**[開始モード]** を `AlwaysRunning` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="d13d1-315">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-315">Select **OK**.</span></span>
  1. <span data-ttu-id="d13d1-316">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="d13d1-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="d13d1-317">アプリを右クリックして、 **[Web サイトの管理]**  >  **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="d13d1-318">既定の **[有効化されたプリロード]** 設定は `False` です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="d13d1-319">**[有効化されたプリロード]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="d13d1-320">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-320">Select **OK**.</span></span>

* <span data-ttu-id="d13d1-321">`web.config` を使う場合は、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの `web.config` ファイルの `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="d13d1-322">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d13d1-322">Idle Timeout</span></span>

<span data-ttu-id="d13d1-323">" *アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="d13d1-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="d13d1-324">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="d13d1-325">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="d13d1-326">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="d13d1-327">既定の **[アイドル状態のタイムアウト (分)]** は `20` 分です。</span><span class="sxs-lookup"><span data-stu-id="d13d1-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="d13d1-328">**[アイドル状態のタイムアウト (分)]** を `0` (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="d13d1-329">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-329">Select **OK**.</span></span>
1. <span data-ttu-id="d13d1-330">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="d13d1-330">Recycle the worker process.</span></span>

<span data-ttu-id="d13d1-331">アプリのホストされている[アウトプロセス](xref:host-and-deploy/iis/out-of-process-hosting)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="d13d1-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="d13d1-332">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="d13d1-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="d13d1-333">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="d13d1-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="d13d1-334">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d13d1-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="d13d1-335">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="d13d1-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="d13d1-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="d13d1-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="d13d1-337">[アプリケーション プールのプロセス モデルの設定 `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="d13d1-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="d13d1-338">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="d13d1-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="d13d1-339">Module</span><span class="sxs-lookup"><span data-stu-id="d13d1-339">Module</span></span>

<span data-ttu-id="d13d1-340">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="d13d1-340">**IIS (x86/amd64)** :</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="d13d1-341">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="d13d1-341">**IIS Express (x86/amd64)** :</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="d13d1-342">Schema</span><span class="sxs-lookup"><span data-stu-id="d13d1-342">Schema</span></span>

<span data-ttu-id="d13d1-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d13d1-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="d13d1-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d13d1-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="d13d1-345">構成</span><span class="sxs-lookup"><span data-stu-id="d13d1-345">Configuration</span></span>

<span data-ttu-id="d13d1-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d13d1-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="d13d1-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d13d1-347">**IIS Express**</span></span>

* <span data-ttu-id="d13d1-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="d13d1-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="d13d1-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="d13d1-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="d13d1-350">ファイルは、`applicationHost.config` ファイルで `aspnetcore` を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="d13d1-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
