---
title: ASP.NET Core に HTTPS を適用する
author: rick-anderson
description: ASP.NET Core web アプリで HTTPS/TLS を要求する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: 3277fda0d1dcb5121a2172b3fc1e4869ed6f8430
ms.sourcegitcommit: fc4cce2767e34f81079510f34bd54e9d0aa86497
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592870"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="ba699-103">ASP.NET Core に HTTPS を適用する</span><span class="sxs-lookup"><span data-stu-id="ba699-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="ba699-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba699-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ba699-105">このドキュメントでは、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba699-105">This document shows how to:</span></span>

* <span data-ttu-id="ba699-106">すべての要求に HTTPS が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba699-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="ba699-107">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ba699-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="ba699-108">API がないと、クライアントが最初の要求で機微なデータを送信できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ba699-109">API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ba699-109">API projects</span></span>
>
> <span data-ttu-id="ba699-110">機密情報を受け取る Web Api では [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="ba699-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ba699-111">`RequireHttpsAttribute` HTTP 状態コードを使用して、HTTP から HTTPS にブラウザーをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ba699-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ba699-112">API クライアントは、HTTP から HTTPS へのリダイレクトについて理解したり、従うことはできません。</span><span class="sxs-lookup"><span data-stu-id="ba699-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ba699-113">このようなクライアントは、HTTP 経由で情報を送信する場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ba699-114">Web Api は次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ba699-115">HTTP でリッスンしません。</span><span class="sxs-lookup"><span data-stu-id="ba699-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ba699-116">状態コード 400 (Bad Request) で接続を閉じ、要求を処理しません。</span><span class="sxs-lookup"><span data-stu-id="ba699-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="ba699-117">HSTS と API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ba699-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="ba699-118">HSTS は一般にブラウザー専用の命令であるため、既定の API プロジェクトには [Hsts](#hsts) は含まれません。</span><span class="sxs-lookup"><span data-stu-id="ba699-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="ba699-119">電話やデスクトップアプリなどの他の呼び出し元は、命令に従い **ません** 。</span><span class="sxs-lookup"><span data-stu-id="ba699-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="ba699-120">ブラウザー内でも、HTTP 経由の API に対して認証された単一の呼び出しにより、セキュリティで保護されていないネットワークに対するリスクが生じます。</span><span class="sxs-lookup"><span data-stu-id="ba699-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="ba699-121">セキュリティで保護された方法は、HTTPS 経由でのみリッスンして応答するように API プロジェクトを構成することです。</span><span class="sxs-lookup"><span data-stu-id="ba699-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ba699-122">API プロジェクト</span><span class="sxs-lookup"><span data-stu-id="ba699-122">API projects</span></span>
>
> <span data-ttu-id="ba699-123">機密情報を受け取る Web Api では [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **を使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="ba699-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ba699-124">`RequireHttpsAttribute` HTTP 状態コードを使用して、HTTP から HTTPS にブラウザーをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="ba699-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ba699-125">API クライアントは、HTTP から HTTPS へのリダイレクトについて理解したり、従うことはできません。</span><span class="sxs-lookup"><span data-stu-id="ba699-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ba699-126">このようなクライアントは、HTTP 経由で情報を送信する場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ba699-127">Web Api は次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ba699-128">HTTP でリッスンしません。</span><span class="sxs-lookup"><span data-stu-id="ba699-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ba699-129">状態コード 400 (Bad Request) で接続を閉じ、要求を処理しません。</span><span class="sxs-lookup"><span data-stu-id="ba699-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="ba699-130">HTTPS を必須にする</span><span class="sxs-lookup"><span data-stu-id="ba699-130">Require HTTPS</span></span>

<span data-ttu-id="ba699-131">Web アプリの運用 ASP.NET Core では次のものを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ba699-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="ba699-132"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>HTTP 要求を https にリダイレクトする Https リダイレクトミドルウェア ()。</span><span class="sxs-lookup"><span data-stu-id="ba699-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="ba699-133">HSTS ミドルウェア ([Usehsts](#http-strict-transport-security-protocol-hsts)) を介して、HTTP Strict Transport Security Protocol (hsts) ヘッダーをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="ba699-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="ba699-134">リバースプロキシ構成で展開されたアプリは、プロキシが接続セキュリティ (HTTPS) を処理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="ba699-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="ba699-135">プロキシが HTTPS リダイレクトも処理する場合は、HTTPS リダイレクトミドルウェアを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ba699-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ba699-136">また、プロキシサーバーが HSTS ヘッダーの書き込みも処理する場合 ( [IIS 10.0 (1709) 以降のネイティブ HSTS のサポート](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)など)、アプリでは Hsts ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ba699-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="ba699-137">詳細については、「 [プロジェクト作成時の HTTPS/HSTS のオプトアウト](#opt-out-of-httpshsts-on-project-creation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="ba699-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="ba699-138">UseHttpsRedirection</span></span>

<span data-ttu-id="ba699-139">次のコードは、 `UseHttpsRedirection` クラス内でを呼び出し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="ba699-140">前の強調表示されているコード:</span><span class="sxs-lookup"><span data-stu-id="ba699-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="ba699-141">既定の [HttpsRedirectionOptions statuscode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba699-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="ba699-142">[](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) `ASPNETCORE_HTTPS_PORT` 環境変数または[iserverHttpsRedirectionOptions 機能](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)でオーバーライドされない限り、既定の (null) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba699-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="ba699-143">永続的なリダイレクトではなく、一時的なリダイレクトを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ba699-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="ba699-144">リンク キャッシュを使用すると、開発環境で不安定な動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="ba699-145">アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、「運用環境 [で永続的なリダイレクトを構成](#configure-permanent-redirects-in-production) する」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="ba699-146">[Hsts](#http-strict-transport-security-protocol-hsts)を使用して、セキュリティで保護されたリソース要求のみをアプリケーションに送信する (運用環境のみ) ことをクライアントに通知することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ba699-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="ba699-147">ポート構成</span><span class="sxs-lookup"><span data-stu-id="ba699-147">Port configuration</span></span>

<span data-ttu-id="ba699-148">ミドルウェアがセキュリティで保護されていない要求を HTTPS にリダイレクトするには、ポートが使用可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="ba699-149">使用可能なポートがない場合:</span><span class="sxs-lookup"><span data-stu-id="ba699-149">If no port is available:</span></span>

* <span data-ttu-id="ba699-150">HTTPS へのリダイレクトは行われません。</span><span class="sxs-lookup"><span data-stu-id="ba699-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="ba699-151">ミドルウェアは、"リダイレクト用の https ポートを特定できませんでした" という警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ba699-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="ba699-152">次のいずれかの方法を使用して、HTTPS ポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="ba699-153">[HttpsRedirectionOptions](#options)を設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="ba699-154">`https_port`[ホスト設定](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port)を設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="ba699-155">ホスト構成。</span><span class="sxs-lookup"><span data-stu-id="ba699-155">In host configuration.</span></span>
  * <span data-ttu-id="ba699-156">環境変数を設定し `ASPNETCORE_HTTPS_PORT` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ba699-157">最上位レベルのエントリをに追加し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ba699-158">[ASPNETCORE_URLS 環境変数](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)を使用して、セキュリティで保護されたスキームのポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="ba699-159">環境変数によってサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="ba699-159">The environment variable configures the server.</span></span> <span data-ttu-id="ba699-160">ミドルウェアは、を使用して HTTPS ポートを間接的に検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ba699-161">リバースプロキシの展開では、この方法は使用できません。</span><span class="sxs-lookup"><span data-stu-id="ba699-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="ba699-162">`https_port`[ホスト設定](xref:fundamentals/host/web-host#https-port)を設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="ba699-163">ホスト構成。</span><span class="sxs-lookup"><span data-stu-id="ba699-163">In host configuration.</span></span>
  * <span data-ttu-id="ba699-164">環境変数を設定し `ASPNETCORE_HTTPS_PORT` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ba699-165">最上位レベルのエントリをに追加し *appsettings.json* ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ba699-166">[ASPNETCORE_URLS 環境変数](xref:fundamentals/host/web-host#server-urls)を使用して、セキュリティで保護されたスキームのポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="ba699-167">環境変数によってサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="ba699-167">The environment variable configures the server.</span></span> <span data-ttu-id="ba699-168">ミドルウェアは、を使用して HTTPS ポートを間接的に検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ba699-169">リバースプロキシの展開では、この方法は使用できません。</span><span class="sxs-lookup"><span data-stu-id="ba699-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="ba699-170">開発では、 *launchsettings.js* で HTTPS URL を設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="ba699-171">IIS Express が使用されている場合は、HTTPS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ba699-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="ba699-172">[Kestrel](xref:fundamentals/servers/kestrel)サーバーまたは[HTTP.sys](xref:fundamentals/servers/httpsys)サーバーの公開エッジデプロイの HTTPS URL エンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="ba699-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="ba699-173">アプリケーションで使用される **HTTPS ポートは1つ** だけです。</span><span class="sxs-lookup"><span data-stu-id="ba699-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="ba699-174">ミドルウェアは、を使用してポートを検出し <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="ba699-175">リバースプロキシ構成でアプリを実行する場合、は <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 使用できません。</span><span class="sxs-lookup"><span data-stu-id="ba699-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="ba699-176">このセクションで説明する他の方法のいずれかを使用して、ポートを設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="ba699-177">Edge の展開</span><span class="sxs-lookup"><span data-stu-id="ba699-177">Edge deployments</span></span> 

<span data-ttu-id="ba699-178">Kestrel または HTTP.sys が公開エッジサーバーとして使用されている場合、Kestrel または HTTP.sys が両方でリッスンするように構成されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="ba699-179">クライアントがリダイレクトされるセキュリティで保護されたポート (通常、運用環境では443、開発では 5001)。</span><span class="sxs-lookup"><span data-stu-id="ba699-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="ba699-180">セキュリティで保護されていないポート (通常、運用環境では80、開発では 5000)。</span><span class="sxs-lookup"><span data-stu-id="ba699-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="ba699-181">セキュリティで保護されていない要求をアプリケーションが受信し、セキュリティで保護されたポートにクライアントをリダイレクトするには、セキュリティで保護されていないポートにクライアントがアクセスできる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="ba699-182">詳細については、「 [Kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration) 」または「」を参照してください <xref:fundamentals/servers/httpsys> 。</span><span class="sxs-lookup"><span data-stu-id="ba699-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="ba699-183">デプロイメント シナリオ</span><span class="sxs-lookup"><span data-stu-id="ba699-183">Deployment scenarios</span></span>

<span data-ttu-id="ba699-184">クライアントとサーバーの間のファイアウォールでは、トラフィック用の通信ポートも開いている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="ba699-185">リバースプロキシ構成で要求が転送される場合は、HTTPS リダイレクトミドルウェアを呼び出す前に、転送された [ヘッダーミドルウェア](xref:host-and-deploy/proxy-load-balancer) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba699-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ba699-186">転送されたヘッダーミドルウェアは、 `Request.Scheme` ヘッダーを使用してを更新し `X-Forwarded-Proto` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="ba699-187">ミドルウェアは、リダイレクト Uri とその他のセキュリティポリシーを正しく動作させることを許可します。</span><span class="sxs-lookup"><span data-stu-id="ba699-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="ba699-188">転送ヘッダーミドルウェアが使用されていない場合、バックエンドアプリは正しいスキームを受信せず、リダイレクトループで終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="ba699-189">一般的なエンドユーザーエラーメッセージは、リダイレクトされた回数が多すぎることを示しています。</span><span class="sxs-lookup"><span data-stu-id="ba699-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="ba699-190">Azure App Service にデプロイする場合は、 [「チュートリアル: 既存のカスタム SSL 証明書を Azure Web Apps にバインドする](/azure/app-service/app-service-web-tutorial-custom-ssl)」のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ba699-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="ba699-191">Options</span><span class="sxs-lookup"><span data-stu-id="ba699-191">Options</span></span>

<span data-ttu-id="ba699-192">次の強調表示されたコードは、 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) を呼び出してミドルウェアオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="ba699-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="ba699-193">を呼び出す `AddHttpsRedirection` 必要があるのは、 `HttpsPort` またはの値を変更することだけです `RedirectStatusCode` 。</span><span class="sxs-lookup"><span data-stu-id="ba699-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="ba699-194">前の強調表示されているコード:</span><span class="sxs-lookup"><span data-stu-id="ba699-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="ba699-195">HttpsRedirectionOptions をに設定し [ます](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 。 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> これは既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba699-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="ba699-196">への割り当てには、クラスのフィールドを使用し <xref:Microsoft.AspNetCore.Http.StatusCodes> `RedirectStatusCode` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="ba699-197">HTTPS ポートを5001に設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="ba699-198">運用環境での永続的なリダイレクトの構成</span><span class="sxs-lookup"><span data-stu-id="ba699-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="ba699-199">ミドルウェアは、既定ですべてのリダイレクトを使用して [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) を送信します。</span><span class="sxs-lookup"><span data-stu-id="ba699-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="ba699-200">アプリが非開発環境にあるときに永続的なリダイレクト状態コードを送信する場合は、非開発環境の条件付きチェックでミドルウェアオプションの構成をラップします。</span><span class="sxs-lookup"><span data-stu-id="ba699-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba699-201">*Startup.cs* でサービスを構成する場合:</span><span class="sxs-lookup"><span data-stu-id="ba699-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ba699-202">*Startup.cs* でサービスを構成する場合:</span><span class="sxs-lookup"><span data-stu-id="ba699-202">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="ba699-203">HTTPS リダイレクトミドルウェアの代替アプローチ</span><span class="sxs-lookup"><span data-stu-id="ba699-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="ba699-204">HTTPS リダイレクトミドルウェア () を使用する代わりに、 `UseHttpsRedirection` URL リライトミドルウェア () を使用することも `AddRedirectToHttps` できます。</span><span class="sxs-lookup"><span data-stu-id="ba699-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="ba699-205">`AddRedirectToHttps` また、リダイレクトの実行時に状態コードとポートを設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ba699-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="ba699-206">詳細については、「 [URL リライトミドルウェア](xref:fundamentals/url-rewriting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="ba699-207">追加のリダイレクト規則を必要とせずに HTTPS にリダイレクトする場合は、このトピックで説明する HTTPS リダイレクトミドルウェア () を使用することをお勧めし `UseHttpsRedirection` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="ba699-208">HTTP Strict Transport Security Protocol (HSTS)</span><span class="sxs-lookup"><span data-stu-id="ba699-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="ba699-209">[Owasp](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)では、 [HTTP Strict Transport Security (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)は、応答ヘッダーを使用して web アプリによって指定されるオプトインセキュリティ拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="ba699-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="ba699-210">[HSTS をサポートするブラウザー](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support)がこのヘッダーを受け取ると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ba699-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="ba699-211">ブラウザーには、HTTP 経由の通信を送信できないようにするドメインの構成が格納されます。</span><span class="sxs-lookup"><span data-stu-id="ba699-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="ba699-212">ブラウザーは、HTTPS 経由のすべての通信を強制的に実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="ba699-213">ブラウザーによって、ユーザーが信頼されていない証明書や無効な証明書を使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="ba699-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="ba699-214">ブラウザーは、ユーザーがこのような証明書を一時的に信頼できるようにするプロンプトを無効にします。</span><span class="sxs-lookup"><span data-stu-id="ba699-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="ba699-215">HSTS はクライアントによって適用されるため、いくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="ba699-216">クライアントは HSTS をサポートしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-216">The client must support HSTS.</span></span>
* <span data-ttu-id="ba699-217">HSTS で HSTS ポリシーを確立するには、少なくとも1つの HTTPS 要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba699-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="ba699-218">アプリケーションは、すべての HTTP 要求を確認し、HTTP 要求をリダイレクトまたは拒否する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="ba699-219">ASP.NET Core 2.1 以降では、拡張メソッドを使用して HSTS を実装し `UseHsts` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="ba699-220">次のコードは、 `UseHsts` アプリが [開発モード](xref:fundamentals/environments)でない場合にを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ba699-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="ba699-221">`UseHsts` HSTS の設定はブラウザーによって高度なキャッシュが可能であるため、開発では推奨されません。</span><span class="sxs-lookup"><span data-stu-id="ba699-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="ba699-222">既定では、は `UseHsts` ローカルループバックアドレスを除外します。</span><span class="sxs-lookup"><span data-stu-id="ba699-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="ba699-223">初めて HTTPS を実装する運用環境では、メソッドのいずれかを使用して、初期の [HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) を小さい値に設定します <xref:System.TimeSpan> 。</span><span class="sxs-lookup"><span data-stu-id="ba699-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="ba699-224">HTTPS インフラストラクチャを HTTP に戻す必要がある場合に備えて、値を時間から1日以内に設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="ba699-225">HTTPS 構成の持続性を確認したら、HSTS の値を増やします。 `max-age` 一般的に使用される値は1年です。</span><span class="sxs-lookup"><span data-stu-id="ba699-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="ba699-226">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ba699-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="ba699-227">ヘッダーのプリロードパラメーターを設定し `Strict-Transport-Security` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="ba699-228">プリロードは [RFC hsts 仕様](https://tools.ietf.org/html/rfc6797)の一部ではありませんが、web ブラウザーでは、新規インストール時に hsts サイトを事前に読み込むことがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ba699-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="ba699-229">詳細については、[https://hstspreload.org/](https://hstspreload.org/) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ba699-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="ba699-230">HSTS ポリシーをホストサブドメインに適用する [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="ba699-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="ba699-231">`max-age`ヘッダーのパラメーターを明示的に `Strict-Transport-Security` 60 日に設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="ba699-232">設定されていない場合、既定値は30日です。</span><span class="sxs-lookup"><span data-stu-id="ba699-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="ba699-233">詳細については、「 [最長有効期間」ディレクティブ](https://tools.ietf.org/html/rfc6797#section-6.1.1)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="ba699-234">`example.com`除外するホストの一覧にを追加します。</span><span class="sxs-lookup"><span data-stu-id="ba699-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="ba699-235">`UseHsts` 次のループバックホストを除外します。</span><span class="sxs-lookup"><span data-stu-id="ba699-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="ba699-236">`localhost` : IPv4 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="ba699-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ba699-237">`127.0.0.1` : IPv4 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="ba699-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ba699-238">`[::1]` : IPv6 ループバックアドレス。</span><span class="sxs-lookup"><span data-stu-id="ba699-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="ba699-239">プロジェクト作成時の HTTPS/HSTS のオプトアウト</span><span class="sxs-lookup"><span data-stu-id="ba699-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="ba699-240">接続セキュリティがネットワークの公開エッジで処理されるバックエンドサービスのシナリオによっては、各ノードで接続セキュリティを構成する必要がない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba699-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="ba699-241">Visual Studio のテンプレートまたは [dotnet new](/dotnet/core/tools/dotnet-new) コマンドから生成された Web アプリでは、 [HTTPS リダイレクト](#require-https) と [hsts](#http-strict-transport-security-protocol-hsts)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="ba699-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="ba699-242">これらのシナリオを必要としないデプロイでは、テンプレートからアプリを作成するときに HTTPS/HSTS をオプトアウトできます。</span><span class="sxs-lookup"><span data-stu-id="ba699-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="ba699-243">HTTPS/HSTS をオプトアウトするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ba699-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba699-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba699-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ba699-245">[ **HTTPS 用に構成** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="ba699-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![[HTTPS 用に構成] チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション] ダイアログボックスが表示されます。](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![[HTTPS 用に構成] チェックボックスがオフになっている新しい ASP.NET Core Web アプリケーション] ダイアログボックスが表示されます。](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="ba699-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba699-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="ba699-249">`--no-https` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ba699-249">Use the `--no-https` option.</span></span> <span data-ttu-id="ba699-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ba699-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="ba699-251">Windows および macOS で ASP.NET Core HTTPS 開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="ba699-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="ba699-252">.NET Core SDK には、HTTPS 開発証明書が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba699-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="ba699-253">証明書は、最初の実行エクスペリエンスの一部としてインストールされます。</span><span class="sxs-lookup"><span data-stu-id="ba699-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="ba699-254">たとえば、は `dotnet --info` 次の出力のバリエーションを生成します。</span><span class="sxs-lookup"><span data-stu-id="ba699-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="ba699-255">.NET Core SDK をインストールすると、ローカル ユーザーの証明書ストアに ASP.NET Core HTTPS 開発証明書がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="ba699-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="ba699-256">証明書はインストールされていますが、信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="ba699-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="ba699-257">証明書を信頼するには、1回限りの手順を実行して dotnet ツールを実行し `dev-certs` ます。</span><span class="sxs-lookup"><span data-stu-id="ba699-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="ba699-258">次のコマンドにより、`dev-certs` ツールに関するヘルプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ba699-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="ba699-259">Docker 用の開発者証明書を設定する方法</span><span class="sxs-lookup"><span data-stu-id="ba699-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="ba699-260">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore.Docs/issues/6199)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="ba699-261">Linux で HTTPS 証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="ba699-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="ba699-262">Linux の手順については、ディストリビューションのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="ba699-263">Windows Subsystem for Linux からの HTTPS 証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="ba699-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="ba699-264">Windows Subsystem for Linux (WSL) は、HTTPS 自己署名証明書を生成します。WSL 証明書を信頼するように Windows 証明書ストアを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ba699-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="ba699-265">WSL によって生成された証明書をエクスポートするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="ba699-266">WSL ウィンドウで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="ba699-267">上記のコマンドは、Linux が Windows の信頼された証明書を使用するように環境変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="ba699-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="ba699-268">証明書に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ba699-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="ba699-269">このセクションでは ASP.NET Core の HTTPS 開発証明書がインストールされ、 [信頼](#trust)されているが、証明書が信頼されていないことを示すブラウザーの警告が表示される場合に役立つ情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="ba699-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="ba699-270">ASP.NET Core HTTPS 開発証明書は [Kestrel](xref:fundamentals/servers/kestrel)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba699-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ba699-271">IIS Express 証明書を修復するには、 [この Stackoverflow](https://stackoverflow.com/a/20048613/502537) の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-271">To repair the IIS Express certificate, see [this Stackoverflow](https://stackoverflow.com/a/20048613/502537) issue.</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="ba699-272">すべてのプラットフォーム-信頼されていない証明書</span><span class="sxs-lookup"><span data-stu-id="ba699-272">All platforms - certificate not trusted</span></span>

<span data-ttu-id="ba699-273">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-273">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ba699-274">開いているすべてのブラウザーインスタンスを閉じます。</span><span class="sxs-lookup"><span data-stu-id="ba699-274">Close any browser instances open.</span></span> <span data-ttu-id="ba699-275">新しいブラウザーウィンドウを開いてアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="ba699-275">Open a new browser window to app.</span></span> <span data-ttu-id="ba699-276">証明書の信頼は、ブラウザーによってキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="ba699-276">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="ba699-277">上記のコマンドは、ほとんどのブラウザーの信頼の問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="ba699-277">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="ba699-278">ブラウザーがまだ証明書を信頼していない場合は、次のプラットフォーム固有の提案に従ってください。</span><span class="sxs-lookup"><span data-stu-id="ba699-278">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="ba699-279">Docker-信頼されていない証明書</span><span class="sxs-lookup"><span data-stu-id="ba699-279">Docker - certificate not trusted</span></span>

* <span data-ttu-id="ba699-280">*C:\Users \{ USER} \AppData\Roaming\ASP.NET\Https* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="ba699-280">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="ba699-281">ソリューションをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="ba699-281">Clean the solution.</span></span> <span data-ttu-id="ba699-282">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="ba699-282">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="ba699-283">開発ツールを再起動します。</span><span class="sxs-lookup"><span data-stu-id="ba699-283">Restart the development tool.</span></span> <span data-ttu-id="ba699-284">たとえば、Visual Studio、Visual Studio Code、Visual Studio for Mac などです。</span><span class="sxs-lookup"><span data-stu-id="ba699-284">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="ba699-285">Windows-信頼されていない証明書</span><span class="sxs-lookup"><span data-stu-id="ba699-285">Windows - certificate not trusted</span></span>

* <span data-ttu-id="ba699-286">証明書ストア内の証明書を確認します。</span><span class="sxs-lookup"><span data-stu-id="ba699-286">Check the certificates in the certificate store.</span></span> <span data-ttu-id="ba699-287">`localhost`との両方に、 `ASP.NET Core HTTPS development certificate` フレンドリ名を持つ証明書が存在する必要があります。 `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="ba699-287">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="ba699-288">個人証明書と信頼されたルート証明機関の両方から、検出されたすべての証明書を削除します。</span><span class="sxs-lookup"><span data-stu-id="ba699-288">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="ba699-289">IIS Express localhost 証明書 **は削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="ba699-289">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="ba699-290">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-290">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ba699-291">開いているすべてのブラウザーインスタンスを閉じます。</span><span class="sxs-lookup"><span data-stu-id="ba699-291">Close any browser instances open.</span></span> <span data-ttu-id="ba699-292">新しいブラウザーウィンドウを開いてアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="ba699-292">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="ba699-293">OS X-信頼されていない証明書</span><span class="sxs-lookup"><span data-stu-id="ba699-293">OS X - certificate not trusted</span></span>

* <span data-ttu-id="ba699-294">キーチェーンアクセスを開きます。</span><span class="sxs-lookup"><span data-stu-id="ba699-294">Open KeyChain Access.</span></span>
* <span data-ttu-id="ba699-295">システムキーチェーンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ba699-295">Select the System keychain.</span></span>
* <span data-ttu-id="ba699-296">Localhost 証明書の存在を確認します。</span><span class="sxs-lookup"><span data-stu-id="ba699-296">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="ba699-297">アイコンにシンボルが含まれていることを確認 `+` し、すべてのユーザーに対して信頼されていることを示します。</span><span class="sxs-lookup"><span data-stu-id="ba699-297">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="ba699-298">システムキーチェーンから証明書を削除します。</span><span class="sxs-lookup"><span data-stu-id="ba699-298">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="ba699-299">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ba699-299">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ba699-300">開いているすべてのブラウザーインスタンスを閉じます。</span><span class="sxs-lookup"><span data-stu-id="ba699-300">Close any browser instances open.</span></span> <span data-ttu-id="ba699-301">新しいブラウザーウィンドウを開いてアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="ba699-301">Open a new browser window to app.</span></span>

<span data-ttu-id="ba699-302">Visual Studio での証明書の問題のトラブルシューティングについては、 [IIS Express (dotnet/AspNetCore #16892) を使用した HTTPS エラー](https://github.com/dotnet/AspNetCore/issues/16892) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-302">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="ba699-303">IIS Express Visual Studio で使用される SSL 証明書</span><span class="sxs-lookup"><span data-stu-id="ba699-303">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="ba699-304">IIS Express 証明書の問題を解決するには、Visual Studio インストーラーで [ **修復** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ba699-304">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="ba699-305">詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/aspnetcore/issues/16892)します。</span><span class="sxs-lookup"><span data-stu-id="ba699-305">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

<a name="trust-ff"></a>

### <a name="firefox-sec_error_inadequate_key_usage-certificate-error"></a><span data-ttu-id="ba699-306">Firefox SEC_ERROR_INADEQUATE_KEY_USAGE 証明書エラー</span><span class="sxs-lookup"><span data-stu-id="ba699-306">Firefox SEC_ERROR_INADEQUATE_KEY_USAGE certificate error</span></span>

<span data-ttu-id="ba699-307">Firefox ブラウザーでは独自の証明書ストアが使用されるため、 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) または [kestrel](xref:fundamentals/servers/kestrel) 開発者の証明書は信頼されません。</span><span class="sxs-lookup"><span data-stu-id="ba699-307">The Firefox browser uses it's own certificate store, and therefore doesn't trust the [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) or [Kestrel](xref:fundamentals/servers/kestrel) developer certificates.</span></span>

<span data-ttu-id="ba699-308">Firefox を IIS Express または Kestrel と共に使用するには、を設定します。  `security.enterprise_roots.enabled` = `true`</span><span class="sxs-lookup"><span data-stu-id="ba699-308">To use Firefox with IIS Express or Kestrel, set  `security.enterprise_roots.enabled` = `true`</span></span>

1. <span data-ttu-id="ba699-309">`about:config`FireFox ブラウザーで「」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ba699-309">Enter `about:config` in the FireFox browser.</span></span>
1. <span data-ttu-id="ba699-310">リスクに同意する場合は、[リスクを受け入れる] を選択し **て続行** します。</span><span class="sxs-lookup"><span data-stu-id="ba699-310">Select **Accept the Risk and Continue** if you accept the risk.</span></span>
1. <span data-ttu-id="ba699-311">[**すべて表示**] を選択</span><span class="sxs-lookup"><span data-stu-id="ba699-311">Select **Show All**</span></span>
1. <span data-ttu-id="ba699-312">一連 `security.enterprise_roots.enabled` = `true`</span><span class="sxs-lookup"><span data-stu-id="ba699-312">Set `security.enterprise_roots.enabled` = `true`</span></span>
1. <span data-ttu-id="ba699-313">Firefox を終了して再起動する</span><span class="sxs-lookup"><span data-stu-id="ba699-313">Exit and restart Firefox</span></span>

<span data-ttu-id="ba699-314">詳細については、「 [Firefox での証明機関 (ca) の設定](https://support.mozilla.org/kb/setting-certificate-authorities-firefox)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba699-314">For more information, see [Setting Up Certificate Authorities (CAs) in Firefox](https://support.mozilla.org/kb/setting-certificate-authorities-firefox).</span></span>

## <a name="additional-information"></a><span data-ttu-id="ba699-315">関連情報</span><span class="sxs-lookup"><span data-stu-id="ba699-315">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="ba699-316">Apache: HTTPS 構成を使用した Linux での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="ba699-316">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="ba699-317">Nginx: HTTPS 構成を使用した Linux でのホスト ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba699-317">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="ba699-318">IIS で SSL を設定する方法</span><span class="sxs-lookup"><span data-stu-id="ba699-318">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="ba699-319">OWASP HSTS ブラウザーサポート</span><span class="sxs-lookup"><span data-stu-id="ba699-319">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
