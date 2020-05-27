---
<span data-ttu-id="ccd8b-101">title: ' Blazor Azure Active Directory B2C ' author: description: monikerRange: ms. author: ms. カスタム: ms. date: no loc: で、アプリケーション ASP.NET Core をセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ccd8b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ccd8b-102">'Blazor'</span></span>
- <span data-ttu-id="ccd8b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ccd8b-103">'Identity'</span></span>
- <span data-ttu-id="ccd8b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ccd8b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ccd8b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ccd8b-105">'Razor'</span></span>
- <span data-ttu-id="ccd8b-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="ccd8b-107">BlazorAzure Active Directory B2C を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="ccd8b-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ccd8b-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ccd8b-109">この記事では、 Blazor 認証に[AZURE ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="ccd8b-110">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="ccd8b-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="ccd8b-111">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="ccd8b-111">Create a tenant</span></span>

<span data-ttu-id="ccd8b-112">[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="ccd8b-113">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-113">Record the following information:</span></span>

* <span data-ttu-id="ccd8b-114">AAD B2C インスタンス (たとえば `https://contoso.b2clogin.com/` 、末尾のスラッシュを含む)</span><span class="sxs-lookup"><span data-stu-id="ccd8b-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="ccd8b-115">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="ccd8b-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="ccd8b-116">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-116">Register a server API app</span></span>

<span data-ttu-id="ccd8b-117">[「チュートリアル: Azure Active Directory B2C でのアプリケーションの登録](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、*サーバー API アプリ*に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="ccd8b-118">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="ccd8b-119">アプリの**名前**を指定します (たとえば、 \*\* Blazor Server AAD B2C\*\*)。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ccd8b-120">**サポートされているアカウントの種類**については、[マルチテナント] オプションを選択します。**組織ディレクトリ内のアカウントまたは任意の id プロバイダーを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="ccd8b-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="ccd8b-121">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="ccd8b-122">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ccd8b-123">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-123">Select **Register**.</span></span>

<span data-ttu-id="ccd8b-124">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-124">Record the following information:</span></span>

* <span data-ttu-id="ccd8b-125">*サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="ccd8b-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="ccd8b-126">ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="ccd8b-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="ccd8b-127">AAD テナントドメイン (たとえば、 `contoso.onmicrosoft.com` ) ドメインは、 &ndash; 登録済みアプリの Azure portal の [**ブランド**化] ブレードで、**発行元ドメイン**として使用できます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="ccd8b-128">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="ccd8b-129">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="ccd8b-130">**[Save and continue]** (保存して続行) を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="ccd8b-131">**スコープ名**(など) を指定 `API.Access` します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ccd8b-132">**管理者の同意の表示名**を指定します (たとえば、 `Access API` )。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="ccd8b-133">**管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="ccd8b-134">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="ccd8b-135">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-135">Select **Add scope**.</span></span>

<span data-ttu-id="ccd8b-136">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-136">Record the following information:</span></span>

* <span data-ttu-id="ccd8b-137">アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)</span><span class="sxs-lookup"><span data-stu-id="ccd8b-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="ccd8b-138">既定のスコープ (例、 `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="ccd8b-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="ccd8b-139">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-139">Register a client app</span></span>

<span data-ttu-id="ccd8b-140">[「チュートリアル: Azure Active Directory B2C でのアプリケーションの登録](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、*クライアントアプリ*に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="ccd8b-141">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="ccd8b-142">アプリの**名前**( \*\* Blazor クライアント AAD B2C\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="ccd8b-143">**サポートされているアカウントの種類**については、[マルチテナント] オプションを選択します。**組織ディレクトリ内のアカウントまたは任意の id プロバイダーを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="ccd8b-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="ccd8b-144">[**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="ccd8b-145">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="ccd8b-146">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="ccd8b-147">IIS Express の場合、アプリのランダムに生成されたポートは、サーバーアプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="ccd8b-148">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="ccd8b-149">「[アプリを作成](#create-the-app)する」セクションにコメントが表示され、ユーザーにリダイレクト URI の更新を IIS Express ように通知します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="ccd8b-150">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-150">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ccd8b-151">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-151">Select **Register**.</span></span>

<span data-ttu-id="ccd8b-152">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-152">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="ccd8b-153">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ccd8b-154">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ccd8b-155">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ccd8b-156">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ccd8b-157">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-157">Select the **Save** button.</span></span>

<span data-ttu-id="ccd8b-158">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-158">In **API permissions**:</span></span>

1. <span data-ttu-id="ccd8b-159">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-159">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="ccd8b-160">[**名前**] 列から*サーバー API アプリ*を選択します ( \*\* Blazor サーバー AAD B2C\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-160">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ccd8b-161">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-161">Open the **API** list.</span></span>
1. <span data-ttu-id="ccd8b-162">API へのアクセスを有効にします (たとえば、 `API.Access` )。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-162">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ccd8b-163">**[アクセス許可の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-163">Select **Add permissions**.</span></span>
1. <span data-ttu-id="ccd8b-164">[ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-164">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="ccd8b-165">**[はい]** をクリックして操作を確定します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-165">Select **Yes** to confirm.</span></span>

<span data-ttu-id="ccd8b-166">[**ホーム**  >  **Azure AD B2C**  >  **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-166">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="ccd8b-167">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-167">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="ccd8b-168">少なくとも、**アプリケーション要求**の  >  **表示名**ユーザー属性を選択して、コンポーネントにを設定します `context.User.Identity.Name` `LoginDisplay` (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-168">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="ccd8b-169">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin` )。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-169">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="ccd8b-170">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-170">Create the app</span></span>

<span data-ttu-id="ccd8b-171">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-171">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="ccd8b-172">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-172">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ccd8b-173">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-173">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="ccd8b-174">アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-174">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="ccd8b-175">さらに、ホストされているテンプレートによって設定されたスコープによって、 Blazor アプリ ID URI ホストが繰り返される場合があります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-175">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="ccd8b-176">コレクションに対して構成されているスコープ `DefaultAccessTokenScopes` が、 `Program.Main` *クライアントアプリ*の (*Program.cs*) で正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-176">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="ccd8b-177">Azure portal では、*クライアントアプリの\*\*\*認証*\*  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="ccd8b-178">*クライアントアプリ*がランダム IIS Express ポートで実行されている場合、アプリケーションのポートは、[**デバッグ**] パネルの*サーバーアプリの*[プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="ccd8b-179">ポートが*クライアントアプリの*既知のポートで事前に構成されていない場合は、Azure portal で*クライアントアプリの*登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="ccd8b-180">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ccd8b-180">Server app configuration</span></span>

<span data-ttu-id="ccd8b-181">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="ccd8b-181">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ccd8b-182">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="ccd8b-182">Authentication package</span></span>

<span data-ttu-id="ccd8b-183">ASP.NET Core Web Api の呼び出しの認証と承認のサポートは、 [AzureADB2C](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="ccd8b-184">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="ccd8b-184">Authentication service support</span></span>

<span data-ttu-id="ccd8b-185">メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-185">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="ccd8b-186">メソッドは、 <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> Azure Active Directory B2C によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-186">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="ccd8b-187"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-187"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="ccd8b-188">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-188">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="ccd8b-189">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-189">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="ccd8b-190">ユーザー. Identity .指定</span><span class="sxs-lookup"><span data-stu-id="ccd8b-190">User.Identity.Name</span></span>

<span data-ttu-id="ccd8b-191">既定では、は `User.Identity.Name` 設定されません。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-191">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="ccd8b-192">要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ccd8b-192">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="ccd8b-193">アプリの設定</span><span class="sxs-lookup"><span data-stu-id="ccd8b-193">App settings</span></span>

<span data-ttu-id="ccd8b-194">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-194">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="ccd8b-195">例:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-195">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="ccd8b-196">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="ccd8b-196">WeatherForecast controller</span></span>

<span data-ttu-id="ccd8b-197">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-197">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="ccd8b-198">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-198">It's **important** to understand that:</span></span>

* <span data-ttu-id="ccd8b-199">この api [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="ccd8b-200">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="ccd8b-201">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ccd8b-201">Client app configuration</span></span>

<span data-ttu-id="ccd8b-202">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="ccd8b-202">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ccd8b-203">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="ccd8b-203">Authentication package</span></span>

<span data-ttu-id="ccd8b-204">個々の B2C アカウント () を使用するようにアプリを作成すると、 `IndividualB2C` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)([Microsoft. authentication. Webas. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-204">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="ccd8b-205">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-205">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ccd8b-206">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-206">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="ccd8b-207">この[Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)パッケージは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージを推移的にアプリに追加します。このパッケージは、アプリケーションに対して推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-207">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="ccd8b-208">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="ccd8b-208">Authentication service support</span></span>

<span data-ttu-id="ccd8b-209"><xref:System.Net.Http.HttpClient>サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-209">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="ccd8b-210">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-210">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="ccd8b-211">ユーザー認証のサポートは、サービスコンテナーに登録され <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> ている拡張メソッドを[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="ccd8b-212">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ccd8b-213">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-213">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="ccd8b-214">メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ccd8b-215">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="ccd8b-216">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-216">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="ccd8b-217">例:</span><span class="sxs-lookup"><span data-stu-id="ccd8b-217">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="ccd8b-218">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="ccd8b-218">Access token scopes</span></span>

<span data-ttu-id="ccd8b-219">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="ccd8b-220">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="ccd8b-221">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="ccd8b-222">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="ccd8b-223">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="ccd8b-224">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ccd8b-225">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-225">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ccd8b-226">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-226">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="ccd8b-227">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="ccd8b-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="ccd8b-228">Index ページ</span><span class="sxs-lookup"><span data-stu-id="ccd8b-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="ccd8b-229">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ccd8b-230">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ccd8b-231">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="ccd8b-232">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ccd8b-233">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="ccd8b-234">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ccd8b-234">Run the app</span></span>

<span data-ttu-id="ccd8b-235">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-235">Run the app from the Server project.</span></span> <span data-ttu-id="ccd8b-236">Visual Studio を使用する場合は、次のいずれかの方法で行います。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="ccd8b-237">ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="ccd8b-238">**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ccd8b-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ccd8b-239">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ccd8b-239">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="ccd8b-240">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="ccd8b-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="ccd8b-241">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="ccd8b-241">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="ccd8b-242">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="ccd8b-242">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
