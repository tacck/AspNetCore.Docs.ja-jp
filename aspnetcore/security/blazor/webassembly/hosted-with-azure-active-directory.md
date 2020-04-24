---
title: Azure Active Directory を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110929"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="0a435-102">Azure Active Directory を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="0a435-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="0a435-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0a435-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="0a435-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="0a435-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="0a435-106">この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [ Blazorアプリケーション](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0a435-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="0a435-107">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="0a435-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="0a435-108">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="0a435-108">Create a tenant</span></span>

<span data-ttu-id="0a435-109">[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="0a435-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="0a435-110">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="0a435-110">Register a server API app</span></span>

<span data-ttu-id="0a435-111">「[クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="0a435-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="0a435-112">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-112">Select **New registration**.</span></span>
1. <span data-ttu-id="0a435-113">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD\*\*)。</span><span class="sxs-lookup"><span data-stu-id="0a435-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="0a435-114">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="0a435-115">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="0a435-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="0a435-116">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="0a435-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="0a435-117">[ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="0a435-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="0a435-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-118">Select **Register**.</span></span>

<span data-ttu-id="0a435-119">[ **API のアクセス許可**] で、 **Microsoft Graph** > **ユーザーを削除します。** アプリはサインインまたはプロファイルへのアクセスを必要としないため、読み取りアクセス許可を削除します。</span><span class="sxs-lookup"><span data-stu-id="0a435-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="0a435-120">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="0a435-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="0a435-121">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="0a435-122">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="0a435-123">**スコープ名**(など`API.Access`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="0a435-124">**管理者の同意の表示名**を指定し`Access API`ます (たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="0a435-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="0a435-125">**管理者の同意の説明**を入力し`Allows the app to access server app API endpoints.`ます (例:)。</span><span class="sxs-lookup"><span data-stu-id="0a435-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="0a435-126">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a435-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="0a435-127">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-127">Select **Add scope**.</span></span>

<span data-ttu-id="0a435-128">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="0a435-128">Record the following information:</span></span>

* <span data-ttu-id="0a435-129">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="0a435-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="0a435-130">アプリ ID URI (たとえば`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`、、、または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="0a435-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="0a435-131">ディレクトリ ID (テナント ID) (など`222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="0a435-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="0a435-132">AAD テナントドメイン (例`contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="0a435-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="0a435-133">既定のスコープ (例、 `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="0a435-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="0a435-134">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="0a435-134">Register a client app</span></span>

<span data-ttu-id="0a435-135">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="0a435-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="0a435-136">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-136">Select **New registration**.</span></span>
1. <span data-ttu-id="0a435-137">アプリの**名前**( \*\* Blazorクライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="0a435-138">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="0a435-139">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="0a435-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="0a435-140">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、の`https://localhost:5001/authentication/login-callback`リダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="0a435-141">[ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="0a435-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="0a435-142">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-142">Select **Register**.</span></span>

<span data-ttu-id="0a435-143">[**認証** > **プラットフォーム構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="0a435-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="0a435-144">の`https://localhost:5001/authentication/login-callback` **リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a435-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="0a435-145">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0a435-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="0a435-146">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="0a435-147">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-147">Select the **Save** button.</span></span>

<span data-ttu-id="0a435-148">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="0a435-148">In **API permissions**:</span></span>

1. <span data-ttu-id="0a435-149">アプリに**Microsoft Graph** > ユーザーがあることを確認**します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="0a435-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="0a435-150">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="0a435-151">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazorサーバー AAD\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="0a435-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="0a435-152">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="0a435-152">Open the **API** list.</span></span>
1. <span data-ttu-id="0a435-153">API へのアクセスを有効にします`API.Access`(たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="0a435-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="0a435-154">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="0a435-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="0a435-155">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0a435-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="0a435-156">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="0a435-157">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば`33333333-3333-3333-3333-333333333333`、)。</span><span class="sxs-lookup"><span data-stu-id="0a435-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="0a435-158">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0a435-158">Create the app</span></span>

<span data-ttu-id="0a435-159">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a435-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="0a435-160">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="0a435-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="0a435-161">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="0a435-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="0a435-162">アプリ ID URI を`app-id-uri`オプションに渡しますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a435-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="0a435-163">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="0a435-163">Server app configuration</span></span>

<span data-ttu-id="0a435-164">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="0a435-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0a435-165">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="0a435-165">Authentication package</span></span>

<span data-ttu-id="0a435-166">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureAD.UI`次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0a435-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="0a435-167">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="0a435-167">Authentication service support</span></span>

<span data-ttu-id="0a435-168">メソッド`AddAuthentication`は、アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="0a435-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="0a435-169">メソッド`AddAzureADBearer`は、Azure Active Directory によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="0a435-170">`UseAuthentication`次`UseAuthorization`のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0a435-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="0a435-171">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="0a435-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="0a435-172">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0a435-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="0a435-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="0a435-173">User.Identity.Name</span></span>

<span data-ttu-id="0a435-174">既定では、サーバーアプリ API は`User.Identity.Name` 、 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`要求の種類 (など`2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="0a435-175">要求の種類から値を受け取るようにアプリを構成するには<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 、で`Startup.ConfigureServices`の[Tokenvalidationparameters. nameclaimtype を構成します。](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) `name`</span><span class="sxs-lookup"><span data-stu-id="0a435-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="0a435-176">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="0a435-176">App settings</span></span>

<span data-ttu-id="0a435-177">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a435-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="0a435-178">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="0a435-178">WeatherForecast controller</span></span>

<span data-ttu-id="0a435-179">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が`[Authorize]`適用された保護された API を公開します。</span><span class="sxs-lookup"><span data-stu-id="0a435-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="0a435-180">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="0a435-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="0a435-181">この`[Authorize]` api コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="0a435-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="0a435-182">Webassembly で使用される属性は`[Authorize]` 、アプリへのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。 Blazor</span><span class="sxs-lookup"><span data-stu-id="0a435-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="0a435-183">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="0a435-183">Client app configuration</span></span>

<span data-ttu-id="0a435-184">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="0a435-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="0a435-185">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="0a435-185">Authentication package</span></span>

<span data-ttu-id="0a435-186">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0a435-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="0a435-187">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0a435-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0a435-188">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="0a435-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="0a435-189">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0a435-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="0a435-190">`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="0a435-191">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="0a435-191">Authentication service support</span></span>

<span data-ttu-id="0a435-192">ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="0a435-193">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0a435-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a435-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="0a435-195">メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="0a435-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="0a435-196">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="0a435-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="0a435-197">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="0a435-197">Access token scopes</span></span>

<span data-ttu-id="0a435-198">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="0a435-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="0a435-199">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="0a435-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="0a435-200">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0a435-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="0a435-201">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="0a435-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="0a435-202">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="0a435-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="0a435-203">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="0a435-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="0a435-204">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="0a435-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="0a435-205">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="0a435-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="0a435-206">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0a435-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="0a435-207">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="0a435-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="0a435-208">Index ページ</span><span class="sxs-lookup"><span data-stu-id="0a435-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="0a435-209">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a435-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="0a435-210">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a435-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="0a435-211">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a435-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="0a435-212">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a435-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="0a435-213">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0a435-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="0a435-214">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="0a435-214">Run the app</span></span>

<span data-ttu-id="0a435-215">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0a435-215">Run the app from the Server project.</span></span> <span data-ttu-id="0a435-216">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="0a435-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0a435-217">その他の資料</span><span class="sxs-lookup"><span data-stu-id="0a435-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="0a435-218">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="0a435-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
