---
title: BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 5e403d3503fb534d5c3db71a7d6831953c60ac28
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776528"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="5ddf8-102">BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="5ddf8-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ddf8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5ddf8-104">この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [ Blazor アプリケーション](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="5ddf8-105">AAD にアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="5ddf8-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="5ddf8-106">Create a tenant</span></span>

<span data-ttu-id="5ddf8-107">[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="5ddf8-108">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-108">Register a server API app</span></span>

<span data-ttu-id="5ddf8-109">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、*サーバー API アプリ*に AAD アプリを登録し、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="5ddf8-110">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="5ddf8-111">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD\*\*)。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="5ddf8-112">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="5ddf8-113">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="5ddf8-114">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="5ddf8-115">[**権限**  >  **を openid に付与する] チェックボックスと [アクセス許可を offline_access する**] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5ddf8-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-116">Select **Register**.</span></span>

<span data-ttu-id="5ddf8-117">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-117">Record the following information:</span></span>

* <span data-ttu-id="5ddf8-118">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="5ddf8-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5ddf8-119">ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="5ddf8-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="5ddf8-120">AAD テナントドメイン (例 `contoso.onmicrosoft.com` :): 登録されているアプリの Azure portal の [**ブランド**化] ブレードで、**発行元ドメイン**としてドメインを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="5ddf8-121">[ **API のアクセス許可**] で、 **Microsoft Graph**ユーザーを削除  >  **します。** アプリはサインインまたはユーザープロファイルのアクセスを必要としないため、読み取りアクセス許可を削除します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="5ddf8-122">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="5ddf8-123">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="5ddf8-124">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="5ddf8-125">**スコープ名**(など) を指定 `API.Access` します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5ddf8-126">**管理者の同意の表示名**を指定します (たとえば、 `Access API` )。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="5ddf8-127">**管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="5ddf8-128">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="5ddf8-129">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-129">Select **Add scope**.</span></span>

<span data-ttu-id="5ddf8-130">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-130">Record the following information:</span></span>

* <span data-ttu-id="5ddf8-131">アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="5ddf8-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="5ddf8-132">既定のスコープ (例、 `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="5ddf8-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="5ddf8-133">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-133">Register a client app</span></span>

<span data-ttu-id="5ddf8-134">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、*クライアントアプリ*に AAD アプリを登録し、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="5ddf8-135">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="5ddf8-136">アプリの**名前**( \*\* Blazor クライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="5ddf8-137">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="5ddf8-138">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="5ddf8-139">[**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5ddf8-140">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5ddf8-141">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5ddf8-142">IIS Express の場合、アプリのランダムに生成されたポートは、サーバーアプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5ddf8-143">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5ddf8-144">「[アプリを作成](#create-the-app)する」セクションにコメントが表示され、ユーザーにリダイレクト URI の更新を IIS Express ように通知します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5ddf8-145">[**権限**  >  **を openid に付与する] チェックボックスと [アクセス許可を offline_access する**] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5ddf8-146">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-146">Select **Register**.</span></span>

<span data-ttu-id="5ddf8-147">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、 `33333333-3333-3333-3333-333333333333` )。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="5ddf8-148">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5ddf8-149">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5ddf8-150">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5ddf8-151">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5ddf8-152">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-152">Select the **Save** button.</span></span>

<span data-ttu-id="5ddf8-153">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-153">In **API permissions**:</span></span>

1. <span data-ttu-id="5ddf8-154">アプリに**Microsoft Graph**ユーザーがあることを確認  >  **します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="5ddf8-155">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="5ddf8-156">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazor サーバー AAD\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="5ddf8-157">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-157">Open the **API** list.</span></span>
1. <span data-ttu-id="5ddf8-158">API へのアクセスを有効にします (たとえば、 `API.Access` )。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5ddf8-159">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="5ddf8-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="5ddf8-160">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="5ddf8-161">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="5ddf8-162">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-162">Create the app</span></span>

<span data-ttu-id="5ddf8-163">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5ddf8-164">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5ddf8-165">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5ddf8-166">アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="5ddf8-167">Azure portal では、*クライアントアプリの\*\*\*認証*\*  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="5ddf8-168">*クライアントアプリ*がランダム IIS Express ポートで実行されている場合、アプリケーションのポートは、[**デバッグ**] パネルの*サーバーアプリの*[プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="5ddf8-169">ポートが*クライアントアプリの*既知のポートで事前に構成されていない場合は、Azure portal で*クライアントアプリの*登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5ddf8-170">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5ddf8-170">Server app configuration</span></span>

<span data-ttu-id="5ddf8-171">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="5ddf8-171">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5ddf8-172">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="5ddf8-172">Authentication package</span></span>

<span data-ttu-id="5ddf8-173">ASP.NET Core Web Api の呼び出しの認証と承認のサポートは、 [AzureAD](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="5ddf8-174">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="5ddf8-174">Authentication service support</span></span>

<span data-ttu-id="5ddf8-175">メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="5ddf8-176">メソッドは、 <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> Azure Active Directory によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="5ddf8-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="5ddf8-178">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="5ddf8-179">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="5ddf8-180">ユーザー. Identity .指定</span><span class="sxs-lookup"><span data-stu-id="5ddf8-180">User.Identity.Name</span></span>

<span data-ttu-id="5ddf8-181">既定では、サーバーアプリ API は、 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類 (など) の値を設定し `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="5ddf8-182">要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="5ddf8-182">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="5ddf8-183">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="5ddf8-183">App settings</span></span>

<span data-ttu-id="5ddf8-184">ファイル*のappsettings.js*には、アクセストークンの検証に使用する JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-184">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="5ddf8-185">例:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="5ddf8-186">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="5ddf8-186">WeatherForecast controller</span></span>

<span data-ttu-id="5ddf8-187">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="5ddf8-188">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="5ddf8-189">この api [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="5ddf8-190">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="5ddf8-191">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5ddf8-191">Client app configuration</span></span>

<span data-ttu-id="5ddf8-192">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="5ddf8-192">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5ddf8-193">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="5ddf8-193">Authentication package</span></span>

<span data-ttu-id="5ddf8-194">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)([Microsoft. authentication. Webas. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="5ddf8-195">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5ddf8-196">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="5ddf8-197">この[Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)パッケージは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージを推移的にアプリに追加します。このパッケージは、アプリケーションに対して推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-197">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="5ddf8-198">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="5ddf8-198">Authentication service support</span></span>

<span data-ttu-id="5ddf8-199"><xref:System.Net.Http.HttpClient>サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="5ddf8-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="5ddf8-201">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 (など `BlazorSample.ServerAPI` ) です。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-201">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="5ddf8-202">ユーザー認証のサポートは、サービスコンテナーに登録され <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> ている拡張メソッドを[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-202">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="5ddf8-203">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-203">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5ddf8-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5ddf8-205">メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-205">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5ddf8-206">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-206">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="5ddf8-207">構成は、ファイル*の wwwroot/appsettings.js*によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-207">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="5ddf8-208">例:</span><span class="sxs-lookup"><span data-stu-id="5ddf8-208">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="5ddf8-209">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="5ddf8-209">Access token scopes</span></span>

<span data-ttu-id="5ddf8-210">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-210">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="5ddf8-211">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-211">Included by default in the sign in request.</span></span>
* <span data-ttu-id="5ddf8-212">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-212">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="5ddf8-213">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-213">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="5ddf8-214">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-214">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="5ddf8-215">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5ddf8-216">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5ddf8-217">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="5ddf8-218">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="5ddf8-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5ddf8-219">Index ページ</span><span class="sxs-lookup"><span data-stu-id="5ddf8-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="5ddf8-220">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5ddf8-221">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5ddf8-222">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="5ddf8-223">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5ddf8-224">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5ddf8-225">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="5ddf8-225">Run the app</span></span>

<span data-ttu-id="5ddf8-226">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-226">Run the app from the Server project.</span></span> <span data-ttu-id="5ddf8-227">Visual Studio を使用する場合は、次のいずれかの方法で行います。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-227">When using Visual Studio, either:</span></span>

* <span data-ttu-id="5ddf8-228">ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-228">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="5ddf8-229">**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="5ddf8-229">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5ddf8-230">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5ddf8-230">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="5ddf8-231">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="5ddf8-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="5ddf8-232">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="5ddf8-232">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
