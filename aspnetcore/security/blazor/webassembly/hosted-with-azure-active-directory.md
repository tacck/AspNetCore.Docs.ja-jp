---
title: BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 6ff95f0c5c925cbafef2b997a6cb23aeb15ff1aa
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153960"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="44422-102">BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="44422-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="44422-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="44422-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="44422-104">この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [ Blazor アプリケーション](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="44422-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="44422-105">AAD にアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="44422-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="44422-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="44422-106">Create a tenant</span></span>

<span data-ttu-id="44422-107">[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="44422-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="44422-108">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="44422-108">Register a server API app</span></span>

<span data-ttu-id="44422-109">「[クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory**アプリの登録領域に*サーバー API アプリ*の AAD アプリを登録し  >  **App registrations**ます。</span><span class="sxs-lookup"><span data-stu-id="44422-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="44422-110">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-110">Select **New registration**.</span></span>
1. <span data-ttu-id="44422-111">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD\*\*)。</span><span class="sxs-lookup"><span data-stu-id="44422-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="44422-112">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="44422-113">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="44422-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="44422-114">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="44422-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="44422-115">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="44422-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="44422-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-116">Select **Register**.</span></span>

<span data-ttu-id="44422-117">[ **API のアクセス許可**] で、 **Microsoft Graph**ユーザーを削除  >  **します。** アプリはサインインまたはプロファイルへのアクセスを必要としないため、読み取りアクセス許可を削除します。</span><span class="sxs-lookup"><span data-stu-id="44422-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="44422-118">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="44422-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="44422-119">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="44422-120">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="44422-121">**スコープ名**(など) を指定 `API.Access` します。</span><span class="sxs-lookup"><span data-stu-id="44422-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="44422-122">**管理者の同意の表示名**を指定します (たとえば、 `Access API` )。</span><span class="sxs-lookup"><span data-stu-id="44422-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="44422-123">**管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。</span><span class="sxs-lookup"><span data-stu-id="44422-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="44422-124">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="44422-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="44422-125">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-125">Select **Add scope**.</span></span>

<span data-ttu-id="44422-126">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="44422-126">Record the following information:</span></span>

* <span data-ttu-id="44422-127">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="44422-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="44422-128">アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="44422-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="44422-129">ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="44422-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="44422-130">AAD テナントドメイン (例 `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="44422-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="44422-131">既定のスコープ (例、 `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="44422-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="44422-132">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="44422-132">Register a client app</span></span>

<span data-ttu-id="44422-133">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory**アプリの登録領域に*クライアントアプリ*の AAD アプリを登録し  >  **App registrations**ます。</span><span class="sxs-lookup"><span data-stu-id="44422-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="44422-134">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-134">Select **New registration**.</span></span>
1. <span data-ttu-id="44422-135">アプリの**名前**( \*\* Blazor クライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="44422-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="44422-136">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="44422-137">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="44422-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="44422-138">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、のリダイレクト uri を指定し `https://localhost:5001/authentication/login-callback` ます。</span><span class="sxs-lookup"><span data-stu-id="44422-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="44422-139">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="44422-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="44422-140">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-140">Select **Register**.</span></span>

<span data-ttu-id="44422-141">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="44422-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="44422-142">の**リダイレクト URI**が存在することを確認 `https://localhost:5001/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="44422-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="44422-143">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="44422-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="44422-144">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="44422-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="44422-145">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-145">Select the **Save** button.</span></span>

<span data-ttu-id="44422-146">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="44422-146">In **API permissions**:</span></span>

1. <span data-ttu-id="44422-147">アプリに**Microsoft Graph**ユーザーがあることを確認  >  **します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="44422-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="44422-148">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="44422-149">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazor サーバー AAD\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="44422-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="44422-150">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="44422-150">Open the **API** list.</span></span>
1. <span data-ttu-id="44422-151">API へのアクセスを有効にします (たとえば、 `API.Access` )。</span><span class="sxs-lookup"><span data-stu-id="44422-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="44422-152">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="44422-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="44422-153">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="44422-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="44422-154">**[はい]** をクリックして操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="44422-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="44422-155">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、 `33333333-3333-3333-3333-333333333333` )。</span><span class="sxs-lookup"><span data-stu-id="44422-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="44422-156">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="44422-156">Create the app</span></span>

<span data-ttu-id="44422-157">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="44422-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="44422-158">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="44422-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="44422-159">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="44422-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="44422-160">アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="44422-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="44422-161">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="44422-161">Server app configuration</span></span>

<span data-ttu-id="44422-162">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="44422-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="44422-163">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="44422-163">Authentication package</span></span>

<span data-ttu-id="44422-164">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureAD.UI` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="44422-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="44422-165">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="44422-165">Authentication service support</span></span>

<span data-ttu-id="44422-166">メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="44422-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="44422-167">メソッドは、 `AddAzureADBearer` Azure Active Directory によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="44422-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="44422-168">`UseAuthentication``UseAuthorization`次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="44422-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="44422-169">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="44422-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="44422-170">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="44422-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="44422-171">ユーザー. Identity .指定</span><span class="sxs-lookup"><span data-stu-id="44422-171">User.Identity.Name</span></span>

<span data-ttu-id="44422-172">既定では、サーバーアプリ API は、 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類 (など) の値を設定し `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ます。</span><span class="sxs-lookup"><span data-stu-id="44422-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="44422-173">要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="44422-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="44422-174">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="44422-174">App settings</span></span>

<span data-ttu-id="44422-175">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="44422-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="44422-176">例:</span><span class="sxs-lookup"><span data-stu-id="44422-176">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="44422-177">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="44422-177">WeatherForecast controller</span></span>

<span data-ttu-id="44422-178">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="44422-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="44422-179">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="44422-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="44422-180">この api `[Authorize]` コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="44422-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="44422-181">`[Authorize]`Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="44422-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="44422-182">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="44422-182">Client app configuration</span></span>

<span data-ttu-id="44422-183">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="44422-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="44422-184">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="44422-184">Authentication package</span></span>

<span data-ttu-id="44422-185">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="44422-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="44422-186">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="44422-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="44422-187">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="44422-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="44422-188">`{VERSION}`前のパッケージ参照のを、この記事に示されているパッケージのバージョンに置き換え `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> ます。</span><span class="sxs-lookup"><span data-stu-id="44422-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="44422-189">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="44422-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="44422-190">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="44422-190">Authentication service support</span></span>

<span data-ttu-id="44422-191">`HttpClient`サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="44422-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="44422-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="44422-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="44422-193">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="44422-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="44422-194">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="44422-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="44422-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="44422-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="44422-196">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="44422-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="44422-197">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="44422-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="44422-198">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="44422-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="44422-199">例:</span><span class="sxs-lookup"><span data-stu-id="44422-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="44422-200">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="44422-200">Access token scopes</span></span>

<span data-ttu-id="44422-201">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="44422-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="44422-202">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="44422-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="44422-203">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="44422-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="44422-204">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="44422-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="44422-205">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="44422-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="44422-206">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="44422-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="44422-207">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="44422-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="44422-208">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="44422-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="44422-209">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="44422-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="44422-210">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="44422-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="44422-211">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="44422-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="44422-212">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="44422-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="44422-213">Index ページ</span><span class="sxs-lookup"><span data-stu-id="44422-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="44422-214">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="44422-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="44422-215">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="44422-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="44422-216">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="44422-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="44422-217">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="44422-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="44422-218">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="44422-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="44422-219">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="44422-219">Run the app</span></span>

<span data-ttu-id="44422-220">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="44422-220">Run the app from the Server project.</span></span> <span data-ttu-id="44422-221">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="44422-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="44422-222">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="44422-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="44422-223">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="44422-223">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="44422-224">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="44422-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
