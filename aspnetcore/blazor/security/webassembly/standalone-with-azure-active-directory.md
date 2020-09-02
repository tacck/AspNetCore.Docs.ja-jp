---
title: Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/08/2020
no-loc:
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 50aed0e0bc9058e54518512b9570ca0fdcde9b4e
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712429"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="0d000-103">Azure Active Directory を使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="0d000-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="0d000-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0d000-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0d000-105">認証用に [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) を使用する [スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法:</span><span class="sxs-lookup"><span data-stu-id="0d000-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="0d000-106">[AAD テナントと Web アプリケーションを作成する](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="0d000-106">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="0d000-107">Azure portal の **[Azure Active Directory]**  >  **[アプリの登録]** 領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="0d000-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="0d000-108">アプリの**名前**を指定します (例: **Blazor スタンドアロン AAD**)。</span><span class="sxs-lookup"><span data-stu-id="0d000-108">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="0d000-109">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0d000-109">Choose a **Supported account types**.</span></span> <span data-ttu-id="0d000-110">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** を選択できます。</span><span class="sxs-lookup"><span data-stu-id="0d000-110">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="0d000-111">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="0d000-111">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="0d000-112">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="0d000-112">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="0d000-113">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="0d000-113">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="0d000-114">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="0d000-114">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="0d000-115">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="0d000-115">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="0d000-116">このトピックの後半で、IIS Express ユーザーにリダイレクト URI を更新するよう促す注意が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-116">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="0d000-117">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="0d000-117">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="0d000-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0d000-118">Select **Register**.</span></span>

<span data-ttu-id="0d000-119">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="0d000-119">Record the following information:</span></span>

* <span data-ttu-id="0d000-120">アプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="0d000-120">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="0d000-121">ディレクトリ (テナント) ID (例: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="0d000-121">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="0d000-122">**[認証]**  >  **[プラットフォーム構成]**  >  **[Web]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="0d000-122">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="0d000-123">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0d000-123">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="0d000-124">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0d000-124">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="0d000-125">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="0d000-125">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="0d000-126">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0d000-126">Select the **Save** button.</span></span>

<span data-ttu-id="0d000-127">空のフォルダーにアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0d000-127">Create the app in an empty folder.</span></span> <span data-ttu-id="0d000-128">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0d000-128">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="0d000-129">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="0d000-129">Placeholder</span></span>   | <span data-ttu-id="0d000-130">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="0d000-130">Azure portal name</span></span>       | <span data-ttu-id="0d000-131">例</span><span class="sxs-lookup"><span data-stu-id="0d000-131">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="0d000-132">アプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="0d000-132">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="0d000-133">ディレクトリ (テナント) ID</span><span class="sxs-lookup"><span data-stu-id="0d000-133">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="0d000-134">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="0d000-134">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="0d000-135">Azure portal では、アプリの **[認証]**  >  **[プラットフォーム構成]**  >  **[Web]**  >  **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="0d000-136">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルのアプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="0d000-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="0d000-137">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="0d000-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="0d000-138">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="0d000-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="0d000-139">AAD ユーザー アカウントを使用してアプリにログインする。</span><span class="sxs-lookup"><span data-stu-id="0d000-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="0d000-140">Microsoft API のアクセス トークンを要求する。</span><span class="sxs-lookup"><span data-stu-id="0d000-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="0d000-141">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="0d000-141">For more information, see:</span></span>
  * [<span data-ttu-id="0d000-142">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="0d000-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="0d000-143">[クイック スタート:Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0d000-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="0d000-144">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="0d000-144">Authentication package</span></span>

<span data-ttu-id="0d000-145">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0d000-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="0d000-146">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0d000-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0d000-147">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="0d000-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="0d000-148">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="0d000-148">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="0d000-149">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-149">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="0d000-150">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="0d000-150">Authentication service support</span></span>

<span data-ttu-id="0d000-151">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-151">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="0d000-152">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-152">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0d000-153">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0d000-153">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="0d000-154"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0d000-154">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="0d000-155">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="0d000-155">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="0d000-156">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="0d000-156">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="0d000-157">例:</span><span class="sxs-lookup"><span data-stu-id="0d000-157">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="0d000-158">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="0d000-158">Access token scopes</span></span>

<span data-ttu-id="0d000-159">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0d000-159">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0d000-160">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の既定のアクセス トークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d000-160">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="0d000-161">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0d000-161">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0d000-162">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="0d000-162">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0d000-163">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="0d000-163">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="0d000-164">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="0d000-164">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="0d000-165">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="0d000-165">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0d000-166">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="0d000-166">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="0d000-167">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d000-167">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0d000-168">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d000-168">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0d000-169">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d000-169">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="0d000-170">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0d000-170">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0d000-171">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0d000-171">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="0d000-172">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="0d000-172">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="0d000-173">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="0d000-173">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
