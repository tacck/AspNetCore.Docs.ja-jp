---
<span data-ttu-id="16c23-101">title: ' Blazor Azure Active Directory B2C ' author: description: monikerRange: ms. author: ms. カスタム: ms. date: no loc: で、アプリケーション ASP.NET Core をセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="16c23-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="16c23-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="16c23-102">'Blazor'</span></span>
- <span data-ttu-id="16c23-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="16c23-103">'Identity'</span></span>
- <span data-ttu-id="16c23-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="16c23-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="16c23-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="16c23-105">'Razor'</span></span>
- <span data-ttu-id="16c23-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="16c23-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="16c23-107">BlazorAzure Active Directory B2C を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="16c23-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="16c23-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="16c23-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="16c23-109">この記事では、 Blazor 認証に[AZURE ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="16c23-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="16c23-110">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="16c23-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="16c23-111">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="16c23-111">Create a tenant</span></span>

<span data-ttu-id="16c23-112">[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="16c23-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="16c23-113">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="16c23-113">Record the following information:</span></span>

* <span data-ttu-id="16c23-114">AAD B2C インスタンス (たとえば `https://contoso.b2clogin.com/` 、末尾のスラッシュを含む)</span><span class="sxs-lookup"><span data-stu-id="16c23-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="16c23-115">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="16c23-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="16c23-116">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="16c23-116">Register a server API app</span></span>

<span data-ttu-id="16c23-117">[「チュートリアル: Azure Active Directory B2C でのアプリケーションの登録](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、*サーバー API アプリ*に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="16c23-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="16c23-118">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="16c23-119">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD B2C\*\*)。</span><span class="sxs-lookup"><span data-stu-id="16c23-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="16c23-120">**サポートされているアカウントの種類**については、[マルチテナント] オプションを選択します。**組織ディレクトリ内のアカウントまたは任意の id プロバイダーを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="16c23-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="16c23-121">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="16c23-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="16c23-122">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="16c23-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="16c23-123">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-123">Select **Register**.</span></span>

<span data-ttu-id="16c23-124">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="16c23-124">Record the following information:</span></span>

* <span data-ttu-id="16c23-125">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="16c23-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="16c23-126">ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="16c23-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="16c23-127">AAD テナントドメイン (たとえば、 `contoso.onmicrosoft.com` ) ドメインは、 &ndash; 登録済みアプリの Azure portal の [**ブランド**化] ブレードで、**発行元ドメイン**として使用できます。</span><span class="sxs-lookup"><span data-stu-id="16c23-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="16c23-128">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="16c23-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="16c23-129">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="16c23-130">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="16c23-131">**スコープ名**(など) を指定 `API.Access` します。</span><span class="sxs-lookup"><span data-stu-id="16c23-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16c23-132">**管理者の同意の表示名**を指定します (たとえば、 `Access API` )。</span><span class="sxs-lookup"><span data-stu-id="16c23-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="16c23-133">**管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。</span><span class="sxs-lookup"><span data-stu-id="16c23-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="16c23-134">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="16c23-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="16c23-135">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-135">Select **Add scope**.</span></span>

<span data-ttu-id="16c23-136">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="16c23-136">Record the following information:</span></span>

* <span data-ttu-id="16c23-137">アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="16c23-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="16c23-138">既定のスコープ (例、 `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="16c23-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="16c23-139">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="16c23-139">Register a client app</span></span>

<span data-ttu-id="16c23-140">[「チュートリアル: Azure Active Directory B2C でのアプリケーションの登録](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、*クライアントアプリ*に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="16c23-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="16c23-141">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="16c23-142">アプリの**名前**( \*\* Blazor クライアント AAD B2C\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="16c23-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="16c23-143">**サポートされているアカウントの種類**については、[マルチテナント] オプションを選択します。**組織ディレクトリ内のアカウントまたは任意の id プロバイダーを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="16c23-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="16c23-144">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定したままにし、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="16c23-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="16c23-145">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="16c23-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="16c23-146">IIS Express の場合、ランダムに生成されたポートは、サーバーアプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="16c23-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="16c23-147">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="16c23-147">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="16c23-148">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-148">Select **Register**.</span></span>

<span data-ttu-id="16c23-149">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="16c23-149">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="16c23-150">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="16c23-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="16c23-151">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="16c23-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="16c23-152">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="16c23-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="16c23-153">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="16c23-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="16c23-154">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-154">Select the **Save** button.</span></span>

<span data-ttu-id="16c23-155">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="16c23-155">In **API permissions**:</span></span>

1. <span data-ttu-id="16c23-156">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="16c23-157">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazor サーバー AAD B2C\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="16c23-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="16c23-158">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="16c23-158">Open the **API** list.</span></span>
1. <span data-ttu-id="16c23-159">API へのアクセスを有効にします (たとえば、 `API.Access` )。</span><span class="sxs-lookup"><span data-stu-id="16c23-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16c23-160">**[アクセス許可の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="16c23-161">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-161">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="16c23-162">**[はい]** をクリックして操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="16c23-162">Select **Yes** to confirm.</span></span>

<span data-ttu-id="16c23-163">[**ホーム**  >  **Azure AD B2C**  >  **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="16c23-163">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="16c23-164">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="16c23-164">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="16c23-165">少なくとも、**アプリケーション要求**の  >  **表示名**ユーザー属性を選択して、コンポーネントにを設定します `context.User.Identity.Name` `LoginDisplay` (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="16c23-165">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="16c23-166">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin` )。</span><span class="sxs-lookup"><span data-stu-id="16c23-166">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="16c23-167">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="16c23-167">Create the app</span></span>

<span data-ttu-id="16c23-168">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="16c23-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="16c23-169">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="16c23-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="16c23-170">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="16c23-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="16c23-171">アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16c23-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="16c23-172">さらに、ホストされているテンプレートによって設定されたスコープによって、 Blazor アプリ ID URI ホストが繰り返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="16c23-172">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="16c23-173">コレクションに対して構成されているスコープ `DefaultAccessTokenScopes` が、 `Program.Main` *クライアントアプリ*の (*Program.cs*) で正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="16c23-173">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="16c23-174">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="16c23-174">Server app configuration</span></span>

<span data-ttu-id="16c23-175">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="16c23-175">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16c23-176">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="16c23-176">Authentication package</span></span>

<span data-ttu-id="16c23-177">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="16c23-177">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="16c23-178">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="16c23-178">Authentication service support</span></span>

<span data-ttu-id="16c23-179">メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="16c23-179">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="16c23-180">メソッドは、 `AddAzureADB2CBearer` Azure Active Directory B2C によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="16c23-180">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="16c23-181">`UseAuthentication``UseAuthorization`次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="16c23-181">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="16c23-182">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="16c23-182">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="16c23-183">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="16c23-183">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="16c23-184">ユーザー. Identity .指定</span><span class="sxs-lookup"><span data-stu-id="16c23-184">User.Identity.Name</span></span>

<span data-ttu-id="16c23-185">既定では、は `User.Identity.Name` 設定されません。</span><span class="sxs-lookup"><span data-stu-id="16c23-185">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="16c23-186">要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="16c23-186">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="16c23-187">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="16c23-187">App settings</span></span>

<span data-ttu-id="16c23-188">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="16c23-188">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="16c23-189">例:</span><span class="sxs-lookup"><span data-stu-id="16c23-189">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="16c23-190">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="16c23-190">WeatherForecast controller</span></span>

<span data-ttu-id="16c23-191">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="16c23-191">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="16c23-192">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="16c23-192">It's **important** to understand that:</span></span>

* <span data-ttu-id="16c23-193">この api `[Authorize]` コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="16c23-193">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="16c23-194">`[Authorize]`Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16c23-194">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="16c23-195">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="16c23-195">Client app configuration</span></span>

<span data-ttu-id="16c23-196">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="16c23-196">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16c23-197">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="16c23-197">Authentication package</span></span>

<span data-ttu-id="16c23-198">個々の B2C アカウント () を使用するようにアプリを作成すると、 `IndividualB2C` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="16c23-198">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="16c23-199">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="16c23-199">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="16c23-200">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="16c23-200">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="16c23-201">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="16c23-201">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="16c23-202">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="16c23-202">Authentication service support</span></span>

<span data-ttu-id="16c23-203">`HttpClient`サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="16c23-203">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="16c23-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16c23-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="16c23-205">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="16c23-205">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="16c23-206">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="16c23-206">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="16c23-207">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16c23-207">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="16c23-208">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="16c23-208">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="16c23-209">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="16c23-209">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="16c23-210">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="16c23-210">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="16c23-211">例:</span><span class="sxs-lookup"><span data-stu-id="16c23-211">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="16c23-212">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="16c23-212">Access token scopes</span></span>

<span data-ttu-id="16c23-213">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="16c23-213">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="16c23-214">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="16c23-214">Included by default in the sign in request.</span></span>
* <span data-ttu-id="16c23-215">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="16c23-215">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="16c23-216">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="16c23-216">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="16c23-217">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="16c23-217">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="16c23-218">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16c23-218">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="16c23-219">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="16c23-219">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="16c23-220">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="16c23-220">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="16c23-221">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="16c23-221">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="16c23-222">Index ページ</span><span class="sxs-lookup"><span data-stu-id="16c23-222">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="16c23-223">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="16c23-223">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="16c23-224">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="16c23-224">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="16c23-225">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="16c23-225">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="16c23-226">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="16c23-226">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="16c23-227">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="16c23-227">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="16c23-228">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="16c23-228">Run the app</span></span>

<span data-ttu-id="16c23-229">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="16c23-229">Run the app from the Server project.</span></span> <span data-ttu-id="16c23-230">Visual Studio を使用する場合は、次のいずれかの方法で行います。</span><span class="sxs-lookup"><span data-stu-id="16c23-230">When using Visual Studio, either:</span></span>

* <span data-ttu-id="16c23-231">ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="16c23-231">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="16c23-232">**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="16c23-232">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="16c23-233">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="16c23-233">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="16c23-234">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="16c23-234">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="16c23-235">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="16c23-235">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="16c23-236">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="16c23-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
