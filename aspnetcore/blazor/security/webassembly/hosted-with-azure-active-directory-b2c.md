---
title: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory B2C でセキュリティ保護する
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 3dfaa043fd2e6bc092c2db828563aeaedaa9d272
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243539"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="70b50-102">ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory B2C でセキュリティ保護する</span><span class="sxs-lookup"><span data-stu-id="70b50-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="70b50-103">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70b50-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70b50-104">この記事では、認証用に [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) を使用する Blazor WebAssembly スタンドアロン アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="70b50-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="70b50-105">AAD B2C でアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="70b50-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="70b50-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="70b50-106">Create a tenant</span></span>

<span data-ttu-id="70b50-107">「[チュートリアル: Azure Active Directory B2C テナントの作成](/azure/active-directory-b2c/tutorial-create-tenant)」のガイダンスに従って、AAD B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="70b50-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="70b50-108">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="70b50-108">Record the following information:</span></span>

* <span data-ttu-id="70b50-109">AAD B2C インスタンス (例: 末尾にスラッシュが含まれている `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="70b50-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="70b50-110">AAD B2C テナント ドメイン (例: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="70b50-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="70b50-111">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="70b50-111">Register a server API app</span></span>

<span data-ttu-id="70b50-112">「[チュートリアル: Azure Active Directory B2C に Web アプリケーションを登録する](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、"*サーバー API アプリ*" 用の AAD アプリを登録した後、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="70b50-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="70b50-113">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="70b50-114">アプリの **[名前]** を指定します (例: **Blazor サーバー AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="70b50-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="70b50-115">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="70b50-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="70b50-116">"*サーバー API アプリ*" の場合、このシナリオでは **[リダイレクト URI]** は必要ないので、ドロップダウンは **[Web]** に設定されたままにして、リダイレクト URI は入力しません。</span><span class="sxs-lookup"><span data-stu-id="70b50-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="70b50-117">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が有効にされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="70b50-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="70b50-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-118">Select **Register**.</span></span>

<span data-ttu-id="70b50-119">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="70b50-119">Record the following information:</span></span>

* <span data-ttu-id="70b50-120">"*サーバー API アプリ*" のアプリケーション ID (クライアント ID) (例: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="70b50-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="70b50-121">ディレクトリ ID (テナント ID) (例: `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="70b50-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="70b50-122">AAD テナント ドメイン (例: `contoso.onmicrosoft.com`): ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。</span><span class="sxs-lookup"><span data-stu-id="70b50-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="70b50-123">**[API の公開]** で:</span><span class="sxs-lookup"><span data-stu-id="70b50-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="70b50-124">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="70b50-125">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="70b50-126">**[スコープ名]** を指定します (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="70b50-127">**[管理者の同意の表示名]** を指定します (例: `Access API`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="70b50-128">**[管理者の同意の説明]** を指定します (例: `Allows the app to access server app API endpoints.`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="70b50-129">**[状態]** が **[有効]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="70b50-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="70b50-130">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-130">Select **Add scope**.</span></span>

<span data-ttu-id="70b50-131">次の情報を記録しておきます。</span><span class="sxs-lookup"><span data-stu-id="70b50-131">Record the following information:</span></span>

* <span data-ttu-id="70b50-132">アプリ ID の URI (例: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`、`api://11111111-1111-1111-1111-111111111111`、または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="70b50-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="70b50-133">既定のスコープ (例: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="70b50-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="70b50-134">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="70b50-134">Register a client app</span></span>

<span data-ttu-id="70b50-135">「[チュートリアル: Azure Active Directory B2C に Web アプリケーションを登録する](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに再度従って "*クライアント アプリ*" に AAD アプリを登録した後、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="70b50-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="70b50-136">**[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="70b50-137">アプリの **[名前]** を指定します (例: **Blazor クライアント AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="70b50-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="70b50-138">**[サポートされているアカウントの種類]** で、マルチテナント オプションを選択します: **[任意の組織ディレクトリ内のアカウントまたは任意の ID プロバイダー。Azure AD B2C でユーザーを認証します。]**</span><span class="sxs-lookup"><span data-stu-id="70b50-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="70b50-139">**[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="70b50-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="70b50-140">Kestrel で実行されているアプリの既定のポートは 5001 です。</span><span class="sxs-lookup"><span data-stu-id="70b50-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="70b50-141">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="70b50-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="70b50-142">IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのサーバー アプリのプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="70b50-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="70b50-143">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="70b50-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="70b50-144">「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。</span><span class="sxs-lookup"><span data-stu-id="70b50-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="70b50-145">**[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** が有効にされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="70b50-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="70b50-146">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-146">Select **Register**.</span></span>

<span data-ttu-id="70b50-147">アプリケーション ID (クライアント ID) を記録しておきます (例: `11111111-1111-1111-1111-111111111111`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="70b50-148">**[認証]**  >  **[プラットフォーム構成]**  >  **[Web]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="70b50-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="70b50-149">**[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="70b50-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="70b50-150">**[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="70b50-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="70b50-151">アプリの残りの既定値は、このエクスペリエンスで使用可能です。</span><span class="sxs-lookup"><span data-stu-id="70b50-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="70b50-152">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-152">Select the **Save** button.</span></span>

<span data-ttu-id="70b50-153">**[API のアクセス許可]** で:</span><span class="sxs-lookup"><span data-stu-id="70b50-153">In **API permissions**:</span></span>

1. <span data-ttu-id="70b50-154">**[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="70b50-155">**[名前]** 列で "*サーバー API アプリ*" を選択します (例: **Blazor サーバー AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="70b50-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="70b50-156">**[API]** の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="70b50-156">Open the **API** list.</span></span>
1. <span data-ttu-id="70b50-157">API へのアクセスを有効にします (例: `API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="70b50-158">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="70b50-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="70b50-159">**[<テナント名> に管理者の同意を与えます]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="70b50-160">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="70b50-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="70b50-161">**[ホーム]**  >  **[Azure AD B2C]**  >  **[ユーザー フロー]** で、次を行います。</span><span class="sxs-lookup"><span data-stu-id="70b50-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="70b50-162">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="70b50-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="70b50-163">少なくとも、 **[アプリケーション要求]**  >  **[表示名]** ユーザー属性を選択して、`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) に `context.User.Identity.Name` を設定します。</span><span class="sxs-lookup"><span data-stu-id="70b50-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="70b50-164">アプリ用に作成されたサインアップおよびサインイン ユーザーフロー名を記録しておきます (例: `B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="70b50-165">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="70b50-165">Create the app</span></span>

<span data-ttu-id="70b50-166">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="70b50-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="70b50-167">出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="70b50-168">また、フォルダー名は、プロジェクトの名前の一部となります。</span><span class="sxs-lookup"><span data-stu-id="70b50-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="70b50-169">`app-id-uri` オプションにはアプリ ID URI を渡します。ただし、クライアント アプリで構成の変更が必要になる場合があることに注意してください。これについては、「[アクセス トークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="70b50-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="70b50-170">さらに、ホストされている Blazor テンプレートによって設定されるスコープでは、アプリ ID URI ホストが繰り返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="70b50-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="70b50-171">`DefaultAccessTokenScopes` コレクションに対して構成されたスコープが "*クライアント アプリ*" の `Program.Main` (`Program.cs`) で正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="70b50-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="70b50-172">Azure portal では、"*クライアント アプリ*" の **[認証]**  >  **[プラットフォーム構成]**  >  **[Web]**  >  **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="70b50-173">"*クライアント アプリ*" がランダムな IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルの "*サーバー アプリ*" のプロパティで確認できます。</span><span class="sxs-lookup"><span data-stu-id="70b50-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="70b50-174">前にポートを "*クライアント アプリ*" の既知のポートで構成しなかった場合は、Azure portal で "*クライアント アプリ*" の登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="70b50-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="70b50-175">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="70b50-175">Server app configuration</span></span>

<span data-ttu-id="70b50-176">"*このセクションは、ソリューションの **`Server`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="70b50-176">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="70b50-177">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="70b50-177">Authentication package</span></span>

<span data-ttu-id="70b50-178">ASP.NET Core Web API の呼び出しの認証と承認のサポートは、[`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="70b50-179">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="70b50-179">Authentication service support</span></span>

<span data-ttu-id="70b50-180">`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="70b50-181"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> メソッドにより、Azure Active Directory B2C によって送信されるトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターが設定されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="70b50-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> により、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="70b50-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="70b50-183">アプリにより、受信要求のトークンの解析と検証が試みられます。</span><span class="sxs-lookup"><span data-stu-id="70b50-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="70b50-184">適切な資格情報なしで保護されたリソースへのアクセスを試みた要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="70b50-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="70b50-185">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="70b50-185">User.Identity.Name</span></span>

<span data-ttu-id="70b50-186">既定では、`User.Identity.Name` は設定されません。</span><span class="sxs-lookup"><span data-stu-id="70b50-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="70b50-187">`name` 要求の種類から値を受け取るようにアプリを構成するには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> の <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> を構成します。</span><span class="sxs-lookup"><span data-stu-id="70b50-187">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="70b50-188">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="70b50-188">App settings</span></span>

<span data-ttu-id="70b50-189">`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="70b50-189">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="70b50-190">例:</span><span class="sxs-lookup"><span data-stu-id="70b50-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="70b50-191">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="70b50-191">WeatherForecast controller</span></span>

<span data-ttu-id="70b50-192">WeatherForecast コントローラー (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がコントローラーに適用されている保護された API が公開されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="70b50-193">次のことを理解しておくことが**重要**です。</span><span class="sxs-lookup"><span data-stu-id="70b50-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="70b50-194">この API コントローラーの [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、この API を不正アクセスから保護する唯一のものです。</span><span class="sxs-lookup"><span data-stu-id="70b50-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="70b50-195">Blazor WebAssembly アプリで使用される [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、アプリが正しく動作するにはユーザーを承認する必要がある、というアプリへのヒントとしてのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="70b50-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="70b50-196">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="70b50-196">Client app configuration</span></span>

<span data-ttu-id="70b50-197">"*このセクションは、ソリューションの **`Client`** アプリに関連しています。* "</span><span class="sxs-lookup"><span data-stu-id="70b50-197">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="70b50-198">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="70b50-198">Authentication package</span></span>

<span data-ttu-id="70b50-199">個人の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="70b50-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="70b50-200">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="70b50-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="70b50-201">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="70b50-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="70b50-202">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージがアプリに推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-202">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="70b50-203">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="70b50-203">Authentication service support</span></span>

<span data-ttu-id="70b50-204">サーバー プロジェクトへの要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="70b50-205">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="70b50-205">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="70b50-206">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.ServerAPI`)。</span><span class="sxs-lookup"><span data-stu-id="70b50-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="70b50-207">ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="70b50-208">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="70b50-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="70b50-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="70b50-210"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="70b50-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="70b50-211">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal の AAD の構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="70b50-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="70b50-212">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-212">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="70b50-213">例:</span><span class="sxs-lookup"><span data-stu-id="70b50-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="70b50-214">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="70b50-214">Access token scopes</span></span>

<span data-ttu-id="70b50-215">既定のアクセス トークン スコープでは、次のようなアクセス トークン スコープの一覧が表されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="70b50-216">サインイン要求に既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="70b50-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="70b50-217">認証直後にアクセス トークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="70b50-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="70b50-218">すべてのスコープは、Azure Active Directory の規則に従って同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="70b50-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="70b50-219">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="70b50-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="70b50-220">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="70b50-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="70b50-221">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="70b50-221">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="70b50-222">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="70b50-222">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="70b50-223">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="70b50-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="70b50-224">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="70b50-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="70b50-225">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="70b50-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="70b50-226">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="70b50-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="70b50-227">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="70b50-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="70b50-228">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="70b50-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="70b50-229">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="70b50-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="70b50-230">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="70b50-230">Run the app</span></span>

<span data-ttu-id="70b50-231">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="70b50-231">Run the app from the Server project.</span></span> <span data-ttu-id="70b50-232">Visual Studio を使用しているときは、次のいずれかを行います。</span><span class="sxs-lookup"><span data-stu-id="70b50-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="70b50-233">ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="70b50-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="70b50-234">**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。</span><span class="sxs-lookup"><span data-stu-id="70b50-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="70b50-235">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="70b50-235">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="70b50-236">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="70b50-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="70b50-237">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="70b50-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="70b50-238">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="70b50-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
