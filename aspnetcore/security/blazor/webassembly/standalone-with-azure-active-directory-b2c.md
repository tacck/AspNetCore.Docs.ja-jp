---
<span data-ttu-id="89dae-101">title: ' Azure Active Directory B2C ' author: description: monikerRange: ms. author: ms. Blazor カスタム: ms. date: no loc: という名前のスタンドアロンアプリをセキュリティで ASP.NET Core 保護します。</span><span class="sxs-lookup"><span data-stu-id="89dae-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89dae-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89dae-102">'Blazor'</span></span>
- <span data-ttu-id="89dae-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89dae-103">'Identity'</span></span>
- <span data-ttu-id="89dae-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89dae-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="89dae-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89dae-105">'Razor'</span></span>
- <span data-ttu-id="89dae-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="89dae-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="89dae-107">BlazorAzure Active Directory B2C を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="89dae-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="89dae-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89dae-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89dae-109">Blazor認証に[AZURE ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="89dae-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="89dae-110">テナントを作成し、Azure Portal で web アプリを登録するには、次のトピックのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="89dae-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="89dae-111">AAD B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="89dae-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="89dae-112">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="89dae-112">Record the following information:</span></span>

* <span data-ttu-id="89dae-113">AAD B2C インスタンス (たとえば `https://contoso.b2clogin.com/` 、末尾にスラッシュが含まれています)。</span><span class="sxs-lookup"><span data-stu-id="89dae-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="89dae-114">AAD B2C テナントドメイン (たとえば、 `contoso.onmicrosoft.com` )。</span><span class="sxs-lookup"><span data-stu-id="89dae-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="89dae-115">[「チュートリアル: Azure Active Directory B2C でのアプリケーションの登録](/azure/active-directory-b2c/tutorial-register-applications)」のガイダンスに従って、*クライアントアプリ*に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="89dae-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="89dae-116">**Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89dae-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="89dae-117">アプリの**名前**を指定します ( \*\* Blazor スタンドアロン AAD B2C\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="89dae-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="89dae-118">**サポートされているアカウントの種類**については、[マルチテナント] オプションを選択します。**組織ディレクトリ内のアカウントまたは任意の id プロバイダーを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="89dae-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="89dae-119">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定したままにし、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="89dae-119">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="89dae-120">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="89dae-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="89dae-121">IIS Express の場合、ランダムに生成されたポートは、アプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="89dae-121">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="89dae-122">**アクセス許可**によって  >  **、管理者求めるプロンプトが openid に付与され offline_access アクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="89dae-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="89dae-123">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89dae-123">Select **Register**.</span></span>

<span data-ttu-id="89dae-124">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="89dae-124">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="89dae-125">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="89dae-125">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="89dae-126">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="89dae-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="89dae-127">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="89dae-127">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="89dae-128">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="89dae-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="89dae-129">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89dae-129">Select the **Save** button.</span></span>

<span data-ttu-id="89dae-130">[**ホーム**  >  **Azure AD B2C**  >  **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="89dae-130">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="89dae-131">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="89dae-131">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="89dae-132">少なくとも、**アプリケーション要求**の  >  **表示名**ユーザー属性を選択して、コンポーネントにを設定します `context.User.Identity.Name` `LoginDisplay` (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="89dae-132">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="89dae-133">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin` )。</span><span class="sxs-lookup"><span data-stu-id="89dae-133">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="89dae-134">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89dae-134">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="89dae-135">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="89dae-135">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="89dae-136">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="89dae-136">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="89dae-137">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="89dae-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="89dae-138">AAD ユーザーアカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="89dae-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="89dae-139">Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="89dae-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="89dae-140">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89dae-140">For more information, see:</span></span>
  * [<span data-ttu-id="89dae-141">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="89dae-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="89dae-142">[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。</span><span class="sxs-lookup"><span data-stu-id="89dae-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="89dae-143">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="89dae-143">Authentication package</span></span>

<span data-ttu-id="89dae-144">個々の B2C アカウント () を使用するようにアプリを作成すると、 `IndividualB2C` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="89dae-144">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="89dae-145">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="89dae-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="89dae-146">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="89dae-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="89dae-147">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="89dae-147">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="89dae-148">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="89dae-148">Authentication service support</span></span>

<span data-ttu-id="89dae-149">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="89dae-149">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="89dae-150">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="89dae-150">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="89dae-151">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="89dae-151">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="89dae-152">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="89dae-152">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="89dae-153">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="89dae-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="89dae-154">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="89dae-154">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="89dae-155">例:</span><span class="sxs-lookup"><span data-stu-id="89dae-155">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="89dae-156">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="89dae-156">Access token scopes</span></span>

<span data-ttu-id="89dae-157">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="89dae-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="89dae-158">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し `MsalProviderOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="89dae-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="89dae-159">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="89dae-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="89dae-160">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="89dae-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="89dae-161">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="89dae-161">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="89dae-162">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="89dae-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="89dae-163">Index ページ</span><span class="sxs-lookup"><span data-stu-id="89dae-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="89dae-164">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="89dae-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="89dae-165">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="89dae-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="89dae-166">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="89dae-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="89dae-167">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="89dae-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="89dae-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="89dae-168">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="89dae-169">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="89dae-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="89dae-170">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="89dae-170">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="89dae-171">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="89dae-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
