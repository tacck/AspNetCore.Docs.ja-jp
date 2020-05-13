---
title: BlazorAzure Active Directory B2C を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e8b1a1f86becb1e9f0affe14a667253bd0ec16bf
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153653"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a41d7-102">BlazorAzure Active Directory B2C を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a41d7-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a41d7-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a41d7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a41d7-104">この記事では、 Blazor 認証に[AZURE ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="a41d7-105">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="a41d7-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a41d7-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="a41d7-106">Create a tenant</span></span>

<span data-ttu-id="a41d7-107">[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成し、次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="a41d7-108">AAD B2C インスタンス (たとえば `https://contoso.b2clogin.com/` 、末尾のスラッシュを含む)</span><span class="sxs-lookup"><span data-stu-id="a41d7-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="a41d7-109">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="a41d7-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a41d7-110">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="a41d7-110">Register a server API app</span></span>

<span data-ttu-id="a41d7-111">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory**アプリの登録領域に*サーバー API アプリ*の AAD アプリを登録し  >  **App registrations**ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a41d7-112">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-112">Select **New registration**.</span></span>
1. <span data-ttu-id="a41d7-113">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD B2C\*\*)。</span><span class="sxs-lookup"><span data-stu-id="a41d7-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a41d7-114">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="a41d7-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a41d7-115">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="a41d7-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a41d7-116">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="a41d7-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a41d7-117">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a41d7-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-118">Select **Register**.</span></span>

<span data-ttu-id="a41d7-119">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="a41d7-120">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a41d7-121">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a41d7-122">**スコープ名**(など) を指定 `API.Access` します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a41d7-123">**管理者の同意の表示名**を指定します (たとえば、 `Access API` )。</span><span class="sxs-lookup"><span data-stu-id="a41d7-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a41d7-124">**管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。</span><span class="sxs-lookup"><span data-stu-id="a41d7-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a41d7-125">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a41d7-126">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-126">Select **Add scope**.</span></span>

<span data-ttu-id="a41d7-127">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-127">Record the following information:</span></span>

* <span data-ttu-id="a41d7-128">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="a41d7-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a41d7-129">アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="a41d7-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a41d7-130">ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="a41d7-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="a41d7-131">*サーバー API アプリ*アプリ ID URI (たとえば、Azure portal では、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` クライアント ID に値が既定で設定される可能性があります)</span><span class="sxs-lookup"><span data-stu-id="a41d7-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="a41d7-132">既定のスコープ (例、 `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="a41d7-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a41d7-133">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="a41d7-133">Register a client app</span></span>

<span data-ttu-id="a41d7-134">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory**アプリの登録領域に*クライアントアプリ*の AAD アプリを登録し  >  **App registrations**ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a41d7-135">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-135">Select **New registration**.</span></span>
1. <span data-ttu-id="a41d7-136">アプリの**名前**( \*\* Blazor クライアント AAD B2C\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="a41d7-137">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="a41d7-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a41d7-138">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="a41d7-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a41d7-139">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、のリダイレクト uri を指定し `https://localhost:5001/authentication/login-callback` ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="a41d7-140">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a41d7-141">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-141">Select **Register**.</span></span>

<span data-ttu-id="a41d7-142">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="a41d7-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a41d7-143">の**リダイレクト URI**が存在することを確認 `https://localhost:5001/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a41d7-144">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a41d7-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a41d7-145">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a41d7-146">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-146">Select the **Save** button.</span></span>

<span data-ttu-id="a41d7-147">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="a41d7-147">In **API permissions**:</span></span>

1. <span data-ttu-id="a41d7-148">アプリに**Microsoft Graph**ユーザーがあることを確認  >  **します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="a41d7-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a41d7-149">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a41d7-150">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazor サーバー AAD B2C\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="a41d7-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a41d7-151">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-151">Open the **API** list.</span></span>
1. <span data-ttu-id="a41d7-152">API へのアクセスを有効にします (たとえば、 `API.Access` )。</span><span class="sxs-lookup"><span data-stu-id="a41d7-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a41d7-153">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="a41d7-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a41d7-154">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="a41d7-155">**[はい]** をクリックして操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="a41d7-156">[**ホーム**  >  **Azure AD B2C**  >  **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="a41d7-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="a41d7-157">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="a41d7-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="a41d7-158">少なくとも、**アプリケーション要求**の  >  **表示名**ユーザー属性を選択して、コンポーネントにを設定します `context.User.Identity.Name` `LoginDisplay` (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="a41d7-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="a41d7-159">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-159">Record the following information:</span></span>

* <span data-ttu-id="a41d7-160">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、 `33333333-3333-3333-3333-333333333333` )。</span><span class="sxs-lookup"><span data-stu-id="a41d7-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="a41d7-161">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin` )。</span><span class="sxs-lookup"><span data-stu-id="a41d7-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a41d7-162">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="a41d7-162">Create the app</span></span>

<span data-ttu-id="a41d7-163">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a41d7-164">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a41d7-165">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a41d7-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a41d7-166">アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41d7-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a41d7-167">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="a41d7-167">Server app configuration</span></span>

<span data-ttu-id="a41d7-168">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="a41d7-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a41d7-169">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a41d7-169">Authentication package</span></span>

<span data-ttu-id="a41d7-170">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a41d7-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a41d7-171">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a41d7-171">Authentication service support</span></span>

<span data-ttu-id="a41d7-172">メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a41d7-173">メソッドは、 `AddAzureADB2CBearer` Azure Active Directory B2C によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="a41d7-174">`UseAuthentication``UseAuthorization`次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="a41d7-175">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a41d7-176">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="a41d7-177">ユーザー. Identity .指定</span><span class="sxs-lookup"><span data-stu-id="a41d7-177">User.Identity.Name</span></span>

<span data-ttu-id="a41d7-178">既定では、は `User.Identity.Name` 設定されません。</span><span class="sxs-lookup"><span data-stu-id="a41d7-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="a41d7-179">要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a41d7-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a41d7-180">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="a41d7-180">App settings</span></span>

<span data-ttu-id="a41d7-181">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a41d7-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="a41d7-182">例:</span><span class="sxs-lookup"><span data-stu-id="a41d7-182">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="a41d7-183">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="a41d7-183">WeatherForecast controller</span></span>

<span data-ttu-id="a41d7-184">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="a41d7-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="a41d7-185">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="a41d7-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="a41d7-186">この api `[Authorize]` コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="a41d7-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a41d7-187">`[Authorize]`Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41d7-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a41d7-188">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="a41d7-188">Client app configuration</span></span>

<span data-ttu-id="a41d7-189">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="a41d7-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a41d7-190">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a41d7-190">Authentication package</span></span>

<span data-ttu-id="a41d7-191">個々の B2C アカウント () を使用するようにアプリを作成すると、 `IndividualB2C` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a41d7-192">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a41d7-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a41d7-193">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a41d7-194">`{VERSION}`前のパッケージ参照のを、この記事に示されているパッケージのバージョンに置き換え `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a41d7-195">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a41d7-196">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a41d7-196">Authentication service support</span></span>

<span data-ttu-id="a41d7-197">`HttpClient`サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a41d7-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a41d7-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a41d7-199">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a41d7-200">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a41d7-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a41d7-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a41d7-202">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a41d7-203">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a41d7-204">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="a41d7-205">例:</span><span class="sxs-lookup"><span data-stu-id="a41d7-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a41d7-206">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="a41d7-206">Access token scopes</span></span>

<span data-ttu-id="a41d7-207">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a41d7-208">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="a41d7-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a41d7-209">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a41d7-210">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41d7-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a41d7-211">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a41d7-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a41d7-212">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="a41d7-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a41d7-213">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a41d7-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a41d7-214">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a41d7-215">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41d7-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a41d7-216">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="a41d7-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a41d7-217">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="a41d7-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="a41d7-218">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="a41d7-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a41d7-219">Index ページ</span><span class="sxs-lookup"><span data-stu-id="a41d7-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a41d7-220">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="a41d7-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a41d7-221">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a41d7-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a41d7-222">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a41d7-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a41d7-223">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a41d7-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a41d7-224">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a41d7-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a41d7-225">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a41d7-225">Run the app</span></span>

<span data-ttu-id="a41d7-226">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-226">Run the app from the Server project.</span></span> <span data-ttu-id="a41d7-227">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="a41d7-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a41d7-228">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a41d7-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="a41d7-229">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="a41d7-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a41d7-230">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="a41d7-230">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a41d7-231">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="a41d7-231">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
