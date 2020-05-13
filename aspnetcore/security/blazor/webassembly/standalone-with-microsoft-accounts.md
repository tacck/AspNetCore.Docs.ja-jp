---
title: BlazorMicrosoft アカウントを使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する
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
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 9fc93cc02129081ac6c777677a0c8d6397724e53
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153578"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="1206c-102">BlazorMicrosoft アカウントを使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="1206c-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="1206c-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1206c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1206c-104">Blazor認証に[Microsoft アカウントと AZURE ACTIVE DIRECTORY (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1206c-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="1206c-105">AAD テナントと web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="1206c-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="1206c-106">Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="1206c-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="1206c-107">1\.</span><span class="sxs-lookup"><span data-stu-id="1206c-107">1\.</span></span> <span data-ttu-id="1206c-108">アプリの**名前**( \*\* Blazor クライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="1206c-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="1206c-109">2\.</span><span class="sxs-lookup"><span data-stu-id="1206c-109">2\.</span></span> <span data-ttu-id="1206c-110">[**サポートされているアカウントの種類**] で、**任意の組織ディレクトリのアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="1206c-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="1206c-111">3 \。</span><span class="sxs-lookup"><span data-stu-id="1206c-111">3\.</span></span> <span data-ttu-id="1206c-112">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、のリダイレクト uri を指定し `https://localhost:5001/authentication/login-callback` ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="1206c-113">4 \。</span><span class="sxs-lookup"><span data-stu-id="1206c-113">4\.</span></span> <span data-ttu-id="1206c-114">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="1206c-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="1206c-115">5 \。</span><span class="sxs-lookup"><span data-stu-id="1206c-115">5\.</span></span> <span data-ttu-id="1206c-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1206c-116">Select **Register**.</span></span>

   <span data-ttu-id="1206c-117">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="1206c-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="1206c-118">1\.</span><span class="sxs-lookup"><span data-stu-id="1206c-118">1\.</span></span> <span data-ttu-id="1206c-119">の**リダイレクト URI**が存在することを確認 `https://localhost:5001/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="1206c-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="1206c-120">2\.</span><span class="sxs-lookup"><span data-stu-id="1206c-120">2\.</span></span> <span data-ttu-id="1206c-121">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="1206c-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="1206c-122">3 \。</span><span class="sxs-lookup"><span data-stu-id="1206c-122">3\.</span></span> <span data-ttu-id="1206c-123">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="1206c-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="1206c-124">4 \。</span><span class="sxs-lookup"><span data-stu-id="1206c-124">4\.</span></span> <span data-ttu-id="1206c-125">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1206c-125">Select the **Save** button.</span></span>

   <span data-ttu-id="1206c-126">アプリケーション ID (クライアント ID) を記録します (たとえば、 `11111111-1111-1111-1111-111111111111` )。</span><span class="sxs-lookup"><span data-stu-id="1206c-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="1206c-127">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1206c-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="1206c-128">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1206c-129">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="1206c-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1206c-130">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="1206c-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="1206c-131">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="1206c-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="1206c-132">アプリが正しく構成されている場合は、スタンドアロンアプリの場合と同じ方法を使用して、Microsoft Api のアクセストークンを要求し Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="1206c-133">詳細については、「[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1206c-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1206c-134">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="1206c-134">Authentication package</span></span>

<span data-ttu-id="1206c-135">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)() のパッケージ参照を自動的に受け取り `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1206c-136">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1206c-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1206c-137">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="1206c-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1206c-138">`{VERSION}`前のパッケージ参照のを、この記事に示されているパッケージのバージョンに置き換え `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1206c-139">パッケージによって、 `Microsoft.Authentication.WebAssembly.Msal` パッケージが推移的 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1206c-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1206c-140">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="1206c-140">Authentication service support</span></span>

<span data-ttu-id="1206c-141">ユーザー認証のサポートは、 `AddMsalAuthentication` パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録され `Microsoft.Authentication.WebAssembly.Msal` ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1206c-142">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なすべてのサービスを設定 Identity します。</span><span class="sxs-lookup"><span data-stu-id="1206c-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1206c-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1206c-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="1206c-144">メソッドは、 `AddMsalAuthentication` コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="1206c-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1206c-145">アプリの構成に必要な値は、アプリを登録するときに Microsoft アカウントの構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="1206c-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="1206c-146">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1206c-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="1206c-147">例:</span><span class="sxs-lookup"><span data-stu-id="1206c-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="1206c-148">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="1206c-148">Access token scopes</span></span>

<span data-ttu-id="1206c-149">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="1206c-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1206c-150">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し `MsalProviderOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="1206c-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1206c-151">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="1206c-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1206c-152">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="1206c-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1206c-153">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="1206c-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1206c-154">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1206c-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1206c-155">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="1206c-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1206c-156">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="1206c-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1206c-157">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="1206c-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1206c-158">Index ページ</span><span class="sxs-lookup"><span data-stu-id="1206c-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1206c-159">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="1206c-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1206c-160">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1206c-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1206c-161">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1206c-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1206c-162">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1206c-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1206c-163">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1206c-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1206c-164">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="1206c-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="1206c-165">クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="1206c-165">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="1206c-166">クイック スタート: Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="1206c-166">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
