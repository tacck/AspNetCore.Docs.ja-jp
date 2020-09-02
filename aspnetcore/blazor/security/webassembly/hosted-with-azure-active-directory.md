---
title: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する
author: guardrex
description: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する方法について説明します。
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 12a2509998bb9b4d56e250518b2db91f73dd0e67
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712416"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="789dc-103">ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="789dc-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="789dc-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="789dc-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="789dc-105">この記事では、認証用に [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) を使用する[ホステッド Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="789dc-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="789dc-106">AAD でアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="789dc-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="789dc-107">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="789dc-107">Create a tenant</span></span>

<span data-ttu-id="789dc-108">「[クイック スタート: テナントを設定する](/azure/active-directory/develop/quickstart-create-new-tenant)」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="789dc-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="789dc-109">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="789dc-109">Register a server API app</span></span>

<span data-ttu-id="789dc-110">「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、"*サーバー API アプリ*" 用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="789dc-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="789dc-111">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="789dc-112">アプリの **[名前]** を指定します (例: **Blazor Server AAD**)。</span><span class="sxs-lookup"><span data-stu-id="789dc-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="789dc-113">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="789dc-114">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="789dc-115">"*サーバー API アプリ*" の場合、このシナリオでは **[リダイレクト URI]** は必要ないので、ドロップダウンは **[Web]** に設定されたままにして、リダイレクト URI は入力しません。</span><span class="sxs-lookup"><span data-stu-id="789dc-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="789dc-116">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="789dc-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="789dc-117">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-117">Select **Register**.</span></span>

<span data-ttu-id="789dc-118">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="789dc-118">Record the following information:</span></span>

* <span data-ttu-id="789dc-119">"*サーバー API アプリ*" のアプリケーション (クライアント) ID (例: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="789dc-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="789dc-120">ディレクトリ (テナント) ID (例: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="789dc-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="789dc-121">AAD プライマリ、パブリッシャー、テナント ドメイン (`contoso.onmicrosoft.com` など):ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="789dc-122">アプリにはサインインまたはユーザー プロファイルのアクセスは必要ないため、 **[API のアクセス許可]** で、**Microsoft Graph** > **User.Read** アクセス許可を削除します。</span><span class="sxs-lookup"><span data-stu-id="789dc-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="789dc-123">**[API の公開]** で:</span><span class="sxs-lookup"><span data-stu-id="789dc-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="789dc-124">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="789dc-125">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="789dc-126">**[スコープ名]** を指定します (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="789dc-127">**[管理者の同意の表示名]** を指定します (例: `Access API`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="789dc-128">**[管理者の同意の説明]** を指定します (例: `Allows the app to access server app API endpoints.`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="789dc-129">**[状態]** が **[有効]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="789dc-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="789dc-130">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-130">Select **Add scope**.</span></span>

<span data-ttu-id="789dc-131">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="789dc-131">Record the following information:</span></span>

* <span data-ttu-id="789dc-132">アプリ ID の URI (例: `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`、`api://41451fa7-82d9-4673-8fa5-69eff5a761fd`、または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="789dc-132">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="789dc-133">スコープ名 (例: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="789dc-133">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="789dc-134">アプリ ID URI では、クライアント アプリにおいて特別な構成が必要になる場合があります。詳細については、このトピックで後述する「[アクセス トークン スコープ](#access-token-scopes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="789dc-134">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="789dc-135">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="789dc-135">Register a client app</span></span>

<span data-ttu-id="789dc-136">「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、"*クライアント アプリ*" 用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="789dc-136">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="789dc-137">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-137">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="789dc-138">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD**)。</span><span class="sxs-lookup"><span data-stu-id="789dc-138">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="789dc-139">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-139">Choose a **Supported account types**.</span></span> <span data-ttu-id="789dc-140">このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-140">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="789dc-141">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="789dc-141">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="789dc-142">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="789dc-142">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="789dc-143">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="789dc-143">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="789dc-144">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのサーバー アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-144">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="789dc-145">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="789dc-145">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="789dc-146">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="789dc-146">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="789dc-147">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="789dc-147">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="789dc-148">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-148">Select **Register**.</span></span>

<span data-ttu-id="789dc-149">"*クライアント アプリ*" のアプリケーション (クライアント) ID を記録しておきます (例: `4369008b-21fa-427c-abaa-9b53bf58e538`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-149">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="789dc-150">**[認証]**  >  **[プラットフォーム構成]**  >  **[Web]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="789dc-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="789dc-151">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="789dc-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="789dc-152">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="789dc-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="789dc-153">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="789dc-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="789dc-154">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-154">Select the **Save** button.</span></span>

<span data-ttu-id="789dc-155">**[API のアクセス許可]** で:</span><span class="sxs-lookup"><span data-stu-id="789dc-155">In **API permissions**:</span></span>

1. <span data-ttu-id="789dc-156">アプリに **Microsoft Graph** > **User.Read** アクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="789dc-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="789dc-157">**[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="789dc-158">**[名前]** 列で "*サーバー API アプリ*" を選択します (例: **Blazor Server AAD**)。</span><span class="sxs-lookup"><span data-stu-id="789dc-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="789dc-159">**[API]** の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="789dc-159">Open the **API** list.</span></span>
1. <span data-ttu-id="789dc-160">API へのアクセスを有効にします (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="789dc-161">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="789dc-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="789dc-162">**[<テナント名> に管理者の同意を与えます]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-162">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="789dc-163">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="789dc-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="789dc-164">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="789dc-164">Create the app</span></span>

<span data-ttu-id="789dc-165">空のフォルダーで、次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="789dc-165">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="789dc-166">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="789dc-166">Placeholder</span></span>                  | <span data-ttu-id="789dc-167">Azure portal での名前</span><span class="sxs-lookup"><span data-stu-id="789dc-167">Azure portal name</span></span>                                     | <span data-ttu-id="789dc-168">例</span><span class="sxs-lookup"><span data-stu-id="789dc-168">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="789dc-169">"*クライアント アプリ*" のアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="789dc-169">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="789dc-170">スコープ名</span><span class="sxs-lookup"><span data-stu-id="789dc-170">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="789dc-171">"*サーバー API アプリ*" のアプリケーション (クライアント) ID</span><span class="sxs-lookup"><span data-stu-id="789dc-171">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="789dc-172">アプリケーション ID URI ([注を参照](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="789dc-172">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="789dc-173">プライマリ、パブリッシャー、テナント ドメイン</span><span class="sxs-lookup"><span data-stu-id="789dc-173">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="789dc-174">ディレクトリ (テナント) ID</span><span class="sxs-lookup"><span data-stu-id="789dc-174">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="789dc-175">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="789dc-175">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="789dc-176">`app-id-uri` オプションにはアプリ ID URI を渡します。ただし、クライアント アプリで構成の変更が必要になる場合があることに注意してください。これについては、「[アクセス トークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="789dc-176">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="789dc-177">Azure portal では、"*クライアント アプリ*" の **[認証]**  >  **[プラットフォーム構成]**  >  **[Web]**  >  **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="789dc-178">"*クライアント アプリ*" がランダムな IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルの "*サーバー API アプリ*" のプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="789dc-179">前にポートを "*クライアント アプリ*" の既知のポートで構成しなかった場合は、Azure portal で "*クライアント アプリ*" の登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="789dc-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="789dc-180">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="789dc-180">Server app configuration</span></span>

<span data-ttu-id="789dc-181">"*このセクションは、ソリューションの **`Server`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="789dc-181">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="789dc-182">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="789dc-182">Authentication package</span></span>

<span data-ttu-id="789dc-183">ASP.NET Core Web API の呼び出しの認証と承認のサポートは、[`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="789dc-184">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-184">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="789dc-185">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="789dc-185">Authentication service support</span></span>

<span data-ttu-id="789dc-186">`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-186">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="789dc-187"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> メソッドにより、Azure Active Directory によって送信されるトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-187">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="789dc-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> により、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="789dc-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="789dc-189">アプリにより、受信要求のトークンの解析と検証が試みられます。</span><span class="sxs-lookup"><span data-stu-id="789dc-189">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="789dc-190">適切な資格情報なしで保護されたリソースへのアクセスを試みた要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="789dc-190">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="789dc-191">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="789dc-191">User.Identity.Name</span></span>

<span data-ttu-id="789dc-192">既定では、サーバー アプリ API により、`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類からの値が `User.Identity.Name` に設定されます (例: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-192">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="789dc-193">`name` 要求の種類から値を受け取るようにアプリを構成するには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> の <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> を構成します。</span><span class="sxs-lookup"><span data-stu-id="789dc-193">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="789dc-194">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="789dc-194">App settings</span></span>

<span data-ttu-id="789dc-195">`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="789dc-195">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="789dc-196">例:</span><span class="sxs-lookup"><span data-stu-id="789dc-196">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="789dc-197">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="789dc-197">WeatherForecast controller</span></span>

<span data-ttu-id="789dc-198">WeatherForecast コントローラー (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がコントローラーに適用されている保護された API が公開されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-198">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="789dc-199">次のことを理解しておくことが**重要**です。</span><span class="sxs-lookup"><span data-stu-id="789dc-199">It's **important** to understand that:</span></span>

* <span data-ttu-id="789dc-200">この API コントローラーの [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、この API を不正アクセスから保護する唯一のものです。</span><span class="sxs-lookup"><span data-stu-id="789dc-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="789dc-201">Blazor WebAssembly アプリで使用される [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、アプリが正しく動作するにはユーザーを承認する必要がある、というアプリへのヒントとしてのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="789dc-201">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="789dc-202">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="789dc-202">Client app configuration</span></span>

<span data-ttu-id="789dc-203">"*このセクションは、ソリューションの **`Client`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="789dc-203">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="789dc-204">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="789dc-204">Authentication package</span></span>

<span data-ttu-id="789dc-205">職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="789dc-205">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="789dc-206">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="789dc-206">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="789dc-207">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="789dc-207">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="789dc-208">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="789dc-209">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-209">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="789dc-210">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="789dc-210">Authentication service support</span></span>

<span data-ttu-id="789dc-211">サーバー プロジェクトへの要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-211">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="789dc-212">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="789dc-212">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="789dc-213">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.ServerAPI`)。</span><span class="sxs-lookup"><span data-stu-id="789dc-213">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="789dc-214">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-214">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="789dc-215">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-215">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="789dc-216">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="789dc-216">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="789dc-217"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="789dc-217">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="789dc-218">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal の AAD の構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-218">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="789dc-219">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-219">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="789dc-220">例:</span><span class="sxs-lookup"><span data-stu-id="789dc-220">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="789dc-221">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="789dc-221">Access token scopes</span></span>

<span data-ttu-id="789dc-222">既定のアクセス トークン スコープでは、次のようなアクセス トークン スコープの一覧が表されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-222">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="789dc-223">サインイン要求に既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="789dc-223">Included by default in the sign in request.</span></span>
* <span data-ttu-id="789dc-224">認証直後にアクセス トークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="789dc-224">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="789dc-225">すべてのスコープは、Azure Active Directory の規則に従って同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="789dc-225">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="789dc-226">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="789dc-226">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="789dc-227">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="789dc-227">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="789dc-228">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="789dc-228">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="789dc-229">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="789dc-229">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="789dc-230">ログイン モード</span><span class="sxs-lookup"><span data-stu-id="789dc-230">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="789dc-231">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="789dc-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="789dc-232">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="789dc-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="789dc-233">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="789dc-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="789dc-234">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="789dc-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="789dc-235">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="789dc-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="789dc-236">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="789dc-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="789dc-237">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="789dc-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="789dc-238">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="789dc-238">Run the app</span></span>

<span data-ttu-id="789dc-239">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="789dc-239">Run the app from the Server project.</span></span> <span data-ttu-id="789dc-240">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="789dc-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="789dc-241">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="789dc-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="789dc-242">**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="789dc-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="789dc-243">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="789dc-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="789dc-244">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="789dc-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="789dc-245">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="789dc-245">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
