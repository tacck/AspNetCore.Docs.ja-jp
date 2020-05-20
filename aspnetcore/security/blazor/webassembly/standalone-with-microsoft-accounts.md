---
<span data-ttu-id="a69fa-101">title: ' Blazor Microsoft アカウントを使用して ASP.NET Core webassembly スタンドアロンアプリをセキュリティで保護する: 説明:: ms. 作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="a69fa-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a69fa-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a69fa-102">'Blazor'</span></span>
- <span data-ttu-id="a69fa-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a69fa-103">'Identity'</span></span>
- <span data-ttu-id="a69fa-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a69fa-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="a69fa-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a69fa-105">'Razor'</span></span>
- <span data-ttu-id="a69fa-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="a69fa-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="a69fa-107">BlazorMicrosoft アカウントを使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a69fa-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="a69fa-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a69fa-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a69fa-109">Blazor認証に[Microsoft アカウントと AZURE ACTIVE DIRECTORY (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a69fa-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="a69fa-110">AAD テナントと web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="a69fa-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="a69fa-111">Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a69fa-112">アプリの**名前**を指定します ( \*\* Blazor スタンドアロン AAD Microsoft アカウント\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="a69fa-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="a69fa-113">[**サポートされているアカウントの種類**] で、**任意の組織ディレクトリのアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="a69fa-114">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定したままにし、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="a69fa-114">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a69fa-115">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="a69fa-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a69fa-116">IIS Express の場合、ランダムに生成されたポートは、アプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="a69fa-116">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="a69fa-117">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a69fa-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a69fa-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-118">Select **Register**.</span></span>

<span data-ttu-id="a69fa-119">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="a69fa-119">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="a69fa-120">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="a69fa-120">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a69fa-121">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-121">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a69fa-122">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="a69fa-122">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a69fa-123">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-123">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a69fa-124">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-124">Select the **Save** button.</span></span>

<span data-ttu-id="a69fa-125">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-125">Create the app.</span></span> <span data-ttu-id="a69fa-126">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-126">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="a69fa-127">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-127">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a69fa-128">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a69fa-128">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="a69fa-129">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="a69fa-129">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a69fa-130">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="a69fa-130">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="a69fa-131">Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-131">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a69fa-132">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a69fa-132">For more information, see:</span></span>
  * [<span data-ttu-id="a69fa-133">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="a69fa-133">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a69fa-134">[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。</span><span class="sxs-lookup"><span data-stu-id="a69fa-134">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a69fa-135">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a69fa-135">Authentication package</span></span>

<span data-ttu-id="a69fa-136">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-136">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a69fa-137">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a69fa-137">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a69fa-138">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-138">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="a69fa-139">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a69fa-140">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a69fa-140">Authentication service support</span></span>

<span data-ttu-id="a69fa-141">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a69fa-142">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a69fa-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a69fa-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a69fa-144">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="a69fa-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a69fa-145">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-145">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a69fa-146">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a69fa-147">例:</span><span class="sxs-lookup"><span data-stu-id="a69fa-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a69fa-148">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="a69fa-148">Access token scopes</span></span>

<span data-ttu-id="a69fa-149">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a69fa-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a69fa-150">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し `MsalProviderOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="a69fa-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="a69fa-151">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a69fa-151">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a69fa-152">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="a69fa-152">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a69fa-153">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="a69fa-153">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="a69fa-154">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="a69fa-154">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a69fa-155">Index ページ</span><span class="sxs-lookup"><span data-stu-id="a69fa-155">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a69fa-156">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="a69fa-156">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a69fa-157">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a69fa-157">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a69fa-158">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a69fa-158">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a69fa-159">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a69fa-159">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a69fa-160">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a69fa-160">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="a69fa-161">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="a69fa-161">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="a69fa-162">クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="a69fa-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="a69fa-163">クイック スタート: Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="a69fa-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
