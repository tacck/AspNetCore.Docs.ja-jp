---
<span data-ttu-id="cb11b-101">title: ' Azure Active Directory ' author: description: monikerRange: ms. author: ms. Blazor カスタム: ms. date: no loc: という名前のスタンドアロンアプリをセキュリティで ASP.NET Core 保護します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cb11b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cb11b-102">'Blazor'</span></span>
- <span data-ttu-id="cb11b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cb11b-103">'Identity'</span></span>
- <span data-ttu-id="cb11b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cb11b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cb11b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cb11b-105">'Razor'</span></span>
- <span data-ttu-id="cb11b-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="cb11b-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="cb11b-107">BlazorAzure Active Directory を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="cb11b-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="cb11b-108">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb11b-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb11b-109">Blazor認証に[AZURE ACTIVE DIRECTORY (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する webassembly アプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cb11b-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="cb11b-110">[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="cb11b-111">Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="cb11b-112">アプリの**名前**を指定します ( \*\* Blazor スタンドアロン AAD\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="cb11b-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="cb11b-113">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="cb11b-114">この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="cb11b-115">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定したままにし、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="cb11b-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cb11b-116">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="cb11b-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cb11b-117">IIS Express の場合、ランダムに生成されたポートは、アプリの [**デバッグ**] パネルの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="cb11b-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="cb11b-118">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="cb11b-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cb11b-119">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-119">Select **Register**.</span></span>

<span data-ttu-id="cb11b-120">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-120">Record the following information:</span></span>

* <span data-ttu-id="cb11b-121">アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="cb11b-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="cb11b-122">ディレクトリ ID (テナント ID) (など `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="cb11b-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="cb11b-123">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="cb11b-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="cb11b-124">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cb11b-125">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="cb11b-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="cb11b-126">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cb11b-127">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-127">Select the **Save** button.</span></span>

<span data-ttu-id="cb11b-128">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-128">Create the app.</span></span> <span data-ttu-id="cb11b-129">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="cb11b-130">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="cb11b-131">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="cb11b-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="cb11b-132">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="cb11b-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="cb11b-133">AAD ユーザーアカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="cb11b-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="cb11b-134">Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="cb11b-135">詳細については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb11b-135">For more information, see:</span></span>
  * [<span data-ttu-id="cb11b-136">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="cb11b-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="cb11b-137">[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。</span><span class="sxs-lookup"><span data-stu-id="cb11b-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="cb11b-138">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="cb11b-138">Authentication package</span></span>

<span data-ttu-id="cb11b-139">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="cb11b-140">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="cb11b-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cb11b-141">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="cb11b-142">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="cb11b-143">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="cb11b-143">Authentication service support</span></span>

<span data-ttu-id="cb11b-144">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="cb11b-145">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cb11b-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb11b-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="cb11b-147">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="cb11b-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cb11b-148">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="cb11b-149">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="cb11b-150">例:</span><span class="sxs-lookup"><span data-stu-id="cb11b-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="cb11b-151">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="cb11b-151">Access token scopes</span></span>

<span data-ttu-id="cb11b-152">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="cb11b-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="cb11b-153">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し `MsalProviderOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="cb11b-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="cb11b-154">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb11b-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="cb11b-155">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="cb11b-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cb11b-156">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="cb11b-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="cb11b-157">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="cb11b-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="cb11b-158">Index ページ</span><span class="sxs-lookup"><span data-stu-id="cb11b-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="cb11b-159">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="cb11b-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="cb11b-160">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cb11b-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="cb11b-161">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cb11b-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="cb11b-162">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cb11b-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cb11b-163">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cb11b-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="cb11b-164">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="cb11b-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="cb11b-165">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="cb11b-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
