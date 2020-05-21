---
<span data-ttu-id="27144-101">title: ' Blazor Microsoft アカウントを使用して ASP.NET Core webassembly スタンドアロンアプリをセキュリティで保護する: 説明:: ms. 作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="27144-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="27144-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="27144-102">'Blazor'</span></span>
- <span data-ttu-id="27144-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="27144-103">'Identity'</span></span>
- <span data-ttu-id="27144-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="27144-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="27144-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="27144-105">'Razor'</span></span>
- <span data-ttu-id="27144-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="27144-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="27144-107">BlazorMicrosoft アカウントを使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="27144-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="27144-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="27144-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="27144-109">Blazor認証に[Microsoft アカウントと AZURE ACTIVE DIRECTORY (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="27144-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="27144-110">AAD テナントと web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="27144-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="27144-111">Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="27144-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="27144-112">アプリの**名前**を指定します ( \*\* Blazor スタンドアロン AAD Microsoft アカウント\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="27144-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="27144-113">[**サポートされているアカウントの種類**] で、**任意の組織ディレクトリのアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="27144-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="27144-114">[**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="27144-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="27144-115">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="27144-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="27144-116">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="27144-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="27144-117">IIS Express の場合、アプリのランダムに生成されたポートは、[**デバッグ**] パネルのアプリの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="27144-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="27144-118">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="27144-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="27144-119">このトピックの後半では、リダイレクト URI を更新するようにユーザーに IIS Express 通知するために、このトピックの後半でコメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="27144-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="27144-120">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="27144-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="27144-121">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="27144-121">Select **Register**.</span></span>

<span data-ttu-id="27144-122">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="27144-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="27144-123">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="27144-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="27144-124">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="27144-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="27144-125">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="27144-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="27144-126">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="27144-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="27144-127">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="27144-127">Select the **Save** button.</span></span>

<span data-ttu-id="27144-128">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="27144-128">Create the app.</span></span> <span data-ttu-id="27144-129">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="27144-129">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="27144-130">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="27144-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="27144-131">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="27144-131">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="27144-132">Azure portal では、アプリの**認証**  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="27144-132">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="27144-133">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは [**デバッグ**] パネルのアプリの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="27144-133">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="27144-134">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="27144-134">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="27144-135">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="27144-135">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="27144-136">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="27144-136">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="27144-137">Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="27144-137">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="27144-138">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27144-138">For more information, see:</span></span>
  * [<span data-ttu-id="27144-139">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="27144-139">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="27144-140">[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。</span><span class="sxs-lookup"><span data-stu-id="27144-140">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="27144-141">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="27144-141">Authentication package</span></span>

<span data-ttu-id="27144-142">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="27144-142">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="27144-143">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="27144-143">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="27144-144">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="27144-144">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="27144-145">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="27144-145">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="27144-146">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="27144-146">Authentication service support</span></span>

<span data-ttu-id="27144-147">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="27144-147">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="27144-148">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="27144-148">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="27144-149">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="27144-149">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="27144-150">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="27144-150">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="27144-151">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="27144-151">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="27144-152">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="27144-152">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="27144-153">例:</span><span class="sxs-lookup"><span data-stu-id="27144-153">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="27144-154">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="27144-154">Access token scopes</span></span>

<span data-ttu-id="27144-155">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="27144-155">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="27144-156">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し `MsalProviderOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="27144-156">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="27144-157">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="27144-157">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="27144-158">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="27144-158">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="27144-159">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="27144-159">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="27144-160">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="27144-160">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="27144-161">Index ページ</span><span class="sxs-lookup"><span data-stu-id="27144-161">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="27144-162">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="27144-162">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="27144-163">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="27144-163">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="27144-164">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="27144-164">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="27144-165">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="27144-165">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="27144-166">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="27144-166">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="27144-167">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="27144-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="27144-168">クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="27144-168">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="27144-169">クイック スタート: Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="27144-169">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
