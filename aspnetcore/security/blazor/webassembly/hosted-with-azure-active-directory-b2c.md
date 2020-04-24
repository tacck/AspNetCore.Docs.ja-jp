---
title: Azure Active Directory B2C を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110981"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="37717-102">Azure Active Directory B2C を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="37717-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="37717-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="37717-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="37717-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="37717-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="37717-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="37717-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="37717-106">この記事では、認証にBlazor [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="37717-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="37717-107">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="37717-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="37717-108">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="37717-108">Create a tenant</span></span>

<span data-ttu-id="37717-109">[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成し、次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="37717-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="37717-110">AAD B2C インスタンス (たとえば`https://contoso.b2clogin.com/`、末尾のスラッシュを含む)</span><span class="sxs-lookup"><span data-stu-id="37717-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="37717-111">テナントドメインの AAD B2C (例`contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="37717-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="37717-112">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="37717-112">Register a server API app</span></span>

<span data-ttu-id="37717-113">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="37717-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="37717-114">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-114">Select **New registration**.</span></span>
1. <span data-ttu-id="37717-115">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD B2C\*\*)。</span><span class="sxs-lookup"><span data-stu-id="37717-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="37717-116">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="37717-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="37717-117">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="37717-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="37717-118">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="37717-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="37717-119">**アクセス許可** > によって **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="37717-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="37717-120">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-120">Select **Register**.</span></span>

<span data-ttu-id="37717-121">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="37717-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="37717-122">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="37717-123">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="37717-124">**スコープ名**(など`API.Access`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="37717-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="37717-125">**管理者の同意の表示名**を指定し`Access API`ます (たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="37717-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="37717-126">**管理者の同意の説明**を入力し`Allows the app to access server app API endpoints.`ます (例:)。</span><span class="sxs-lookup"><span data-stu-id="37717-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="37717-127">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="37717-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="37717-128">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-128">Select **Add scope**.</span></span>

<span data-ttu-id="37717-129">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="37717-129">Record the following information:</span></span>

* <span data-ttu-id="37717-130">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="37717-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="37717-131">アプリ ID URI (たとえば`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`、、、または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="37717-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="37717-132">ディレクトリ ID (テナント ID) (など`222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="37717-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="37717-133">*サーバー API アプリ*アプリ ID URI (たとえば`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`、Azure portal では、クライアント ID に値が既定で設定される可能性があります)</span><span class="sxs-lookup"><span data-stu-id="37717-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="37717-134">既定のスコープ (例、 `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="37717-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="37717-135">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="37717-135">Register a client app</span></span>

<span data-ttu-id="37717-136">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="37717-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="37717-137">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-137">Select **New registration**.</span></span>
1. <span data-ttu-id="37717-138">アプリの**名前**( \*\* Blazorクライアント AAD B2C\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="37717-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="37717-139">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="37717-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="37717-140">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="37717-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="37717-141">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、の`https://localhost:5001/authentication/login-callback`リダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="37717-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="37717-142">**アクセス許可** > によって **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="37717-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="37717-143">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-143">Select **Register**.</span></span>

<span data-ttu-id="37717-144">[**認証** > **プラットフォーム構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="37717-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="37717-145">の`https://localhost:5001/authentication/login-callback` **リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="37717-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="37717-146">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="37717-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="37717-147">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="37717-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="37717-148">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-148">Select the **Save** button.</span></span>

<span data-ttu-id="37717-149">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="37717-149">In **API permissions**:</span></span>

1. <span data-ttu-id="37717-150">アプリに**Microsoft Graph** > ユーザーがあることを確認**します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="37717-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="37717-151">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="37717-152">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazorサーバー AAD B2C\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="37717-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="37717-153">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="37717-153">Open the **API** list.</span></span>
1. <span data-ttu-id="37717-154">API へのアクセスを有効にします`API.Access`(たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="37717-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="37717-155">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="37717-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="37717-156">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="37717-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="37717-157">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="37717-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="37717-158">[**ホーム** > **Azure AD B2C** > **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="37717-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="37717-159">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="37717-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="37717-160">少なくとも、**アプリケーション要求** > の**表示名**ユーザー属性を選択して`context.User.Identity.Name` 、 `LoginDisplay`コンポーネントにを設定します (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="37717-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="37717-161">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="37717-161">Record the following information:</span></span>

* <span data-ttu-id="37717-162">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば`33333333-3333-3333-3333-333333333333`、)。</span><span class="sxs-lookup"><span data-stu-id="37717-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="37717-163">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="37717-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="37717-164">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="37717-164">Create the app</span></span>

<span data-ttu-id="37717-165">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="37717-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="37717-166">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="37717-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="37717-167">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="37717-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="37717-168">アプリ ID URI を`app-id-uri`オプションに渡しますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="37717-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="37717-169">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="37717-169">Server app configuration</span></span>

<span data-ttu-id="37717-170">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="37717-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="37717-171">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="37717-171">Authentication package</span></span>

<span data-ttu-id="37717-172">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="37717-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="37717-173">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="37717-173">Authentication service support</span></span>

<span data-ttu-id="37717-174">メソッド`AddAuthentication`は、アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="37717-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="37717-175">メソッド`AddAzureADB2CBearer`は、Azure Active Directory B2C によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="37717-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="37717-176">`UseAuthentication`次`UseAuthorization`のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="37717-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="37717-177">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="37717-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="37717-178">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="37717-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="37717-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="37717-179">User.Identity.Name</span></span>

<span data-ttu-id="37717-180">既定では、 `User.Identity.Name`は設定されません。</span><span class="sxs-lookup"><span data-stu-id="37717-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="37717-181">要求の種類から値を受け取るようにアプリを構成するには<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 、で`Startup.ConfigureServices`の[Tokenvalidationparameters. nameclaimtype を構成します。](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) `name`</span><span class="sxs-lookup"><span data-stu-id="37717-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="37717-182">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="37717-182">App settings</span></span>

<span data-ttu-id="37717-183">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="37717-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="37717-184">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="37717-184">WeatherForecast controller</span></span>

<span data-ttu-id="37717-185">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が`[Authorize]`適用された保護された API を公開します。</span><span class="sxs-lookup"><span data-stu-id="37717-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="37717-186">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="37717-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="37717-187">この`[Authorize]` api コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="37717-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="37717-188">Webassembly で使用される属性は`[Authorize]` 、アプリへのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。 Blazor</span><span class="sxs-lookup"><span data-stu-id="37717-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="37717-189">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="37717-189">Client app configuration</span></span>

<span data-ttu-id="37717-190">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="37717-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="37717-191">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="37717-191">Authentication package</span></span>

<span data-ttu-id="37717-192">個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="37717-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="37717-193">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="37717-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="37717-194">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="37717-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="37717-195">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="37717-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="37717-196">`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="37717-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="37717-197">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="37717-197">Authentication service support</span></span>

<span data-ttu-id="37717-198">ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="37717-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="37717-199">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="37717-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="37717-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="37717-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="37717-201">メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="37717-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="37717-202">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="37717-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="37717-203">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="37717-203">Access token scopes</span></span>

<span data-ttu-id="37717-204">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="37717-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="37717-205">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="37717-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="37717-206">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="37717-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="37717-207">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="37717-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="37717-208">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="37717-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="37717-209">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="37717-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="37717-210">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="37717-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="37717-211">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="37717-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="37717-212">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="37717-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="37717-213">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="37717-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="37717-214">Index ページ</span><span class="sxs-lookup"><span data-stu-id="37717-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="37717-215">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="37717-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="37717-216">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="37717-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="37717-217">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="37717-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="37717-218">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="37717-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="37717-219">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="37717-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="37717-220">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="37717-220">Run the app</span></span>

<span data-ttu-id="37717-221">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="37717-221">Run the app from the Server project.</span></span> <span data-ttu-id="37717-222">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="37717-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="37717-223">その他の資料</span><span class="sxs-lookup"><span data-stu-id="37717-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="37717-224">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="37717-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="37717-225">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="37717-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
