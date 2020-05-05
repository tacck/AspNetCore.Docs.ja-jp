---
title: Azure Active Directory を使用Blazorして ASP.NET Core のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 77584c7102fd2b288227877a052addb9f2296fa6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776657"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="f0961-102">Azure Active Directory を使用Blazorして ASP.NET Core のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="f0961-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="f0961-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0961-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="f0961-104">認証にBlazor [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する webassembly アプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f0961-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="f0961-105">[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。</span><span class="sxs-lookup"><span data-stu-id="f0961-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="f0961-106">Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="f0961-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="f0961-107">アプリの**名前**( \*\* Blazorクライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="f0961-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="f0961-108">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0961-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="f0961-109">この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。</span><span class="sxs-lookup"><span data-stu-id="f0961-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="f0961-110">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、の`https://localhost:5001/authentication/login-callback`リダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="f0961-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="f0961-111">[ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="f0961-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f0961-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0961-112">Select **Register**.</span></span>

<span data-ttu-id="f0961-113">[**認証** > **プラットフォーム構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="f0961-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f0961-114">の`https://localhost:5001/authentication/login-callback` **リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="f0961-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f0961-115">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="f0961-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f0961-116">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="f0961-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f0961-117">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f0961-117">Select the **Save** button.</span></span>

<span data-ttu-id="f0961-118">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="f0961-118">Record the following information:</span></span>

* <span data-ttu-id="f0961-119">アプリケーション ID (クライアント ID) (など`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="f0961-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="f0961-120">ディレクトリ ID (テナント ID) (など`22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="f0961-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="f0961-121">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f0961-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="f0961-122">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="f0961-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f0961-123">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="f0961-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f0961-124">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="f0961-124">Authentication package</span></span>

<span data-ttu-id="f0961-125">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f0961-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f0961-126">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f0961-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f0961-127">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="f0961-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f0961-128">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f0961-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f0961-129">`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f0961-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f0961-130">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="f0961-130">Authentication service support</span></span>

<span data-ttu-id="f0961-131">ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="f0961-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f0961-132">このメソッドは、アプリがIdentityプロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="f0961-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f0961-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0961-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="f0961-134">メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="f0961-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f0961-135">アプリの構成に必要な値は、アプリを登録するときに Microsoft アカウントの構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="f0961-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="f0961-136">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f0961-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f0961-137">例:</span><span class="sxs-lookup"><span data-stu-id="f0961-137">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="f0961-138">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="f0961-138">Access token scopes</span></span>

<span data-ttu-id="f0961-139">Webassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではBlazorありません。</span><span class="sxs-lookup"><span data-stu-id="f0961-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f0961-140">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="f0961-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="f0961-141">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="f0961-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="f0961-142">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="f0961-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="f0961-143">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="f0961-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="f0961-144">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f0961-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f0961-145">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="f0961-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f0961-146">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="f0961-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="f0961-147">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="f0961-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f0961-148">Index ページ</span><span class="sxs-lookup"><span data-stu-id="f0961-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f0961-149">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="f0961-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f0961-150">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f0961-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f0961-151">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f0961-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f0961-152">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f0961-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f0961-153">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f0961-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f0961-154">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="f0961-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
