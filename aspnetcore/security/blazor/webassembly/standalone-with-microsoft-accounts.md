---
title: Microsoft アカウントをBlazor使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111189"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="303b9-102">Microsoft アカウントをBlazor使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="303b9-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="303b9-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="303b9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="303b9-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="303b9-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="303b9-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="303b9-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="303b9-106">認証にBlazor [Microsoft アカウントと Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="303b9-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="303b9-107">AAD テナントと web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="303b9-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="303b9-108">Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="303b9-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="303b9-109">1 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-109">1\.</span></span> <span data-ttu-id="303b9-110">アプリの**名前**( \*\* Blazorクライアント AAD\*\*など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="303b9-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="303b9-111">2 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-111">2\.</span></span> <span data-ttu-id="303b9-112">[**サポートされているアカウントの種類**] で、**任意の組織ディレクトリのアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="303b9-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="303b9-113">3 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-113">3\.</span></span> <span data-ttu-id="303b9-114">[**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、の`https://localhost:5001/authentication/login-callback`リダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="303b9-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="303b9-115">4 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-115">4\.</span></span> <span data-ttu-id="303b9-116">[ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="303b9-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="303b9-117">5 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-117">5\.</span></span> <span data-ttu-id="303b9-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="303b9-118">Select **Register**.</span></span>

   <span data-ttu-id="303b9-119">[**認証** > **プラットフォーム構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="303b9-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="303b9-120">1 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-120">1\.</span></span> <span data-ttu-id="303b9-121">の`https://localhost:5001/authentication/login-callback` **リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="303b9-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="303b9-122">2 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-122">2\.</span></span> <span data-ttu-id="303b9-123">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="303b9-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="303b9-124">3 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-124">3\.</span></span> <span data-ttu-id="303b9-125">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="303b9-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="303b9-126">4 \。</span><span class="sxs-lookup"><span data-stu-id="303b9-126">4\.</span></span> <span data-ttu-id="303b9-127">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="303b9-127">Select the **Save** button.</span></span>

   <span data-ttu-id="303b9-128">アプリケーション ID (クライアント ID) を記録します (たとえば`11111111-1111-1111-1111-111111111111`、)。</span><span class="sxs-lookup"><span data-stu-id="303b9-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="303b9-129">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="303b9-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="303b9-130">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="303b9-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="303b9-131">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="303b9-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="303b9-132">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="303b9-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="303b9-133">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="303b9-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="303b9-134">アプリが正しく構成されている場合は、スタンドアロンBlazorアプリの場合と同じ方法を使用して、Microsoft api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="303b9-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="303b9-135">詳細については、「[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="303b9-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="303b9-136">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="303b9-136">Authentication package</span></span>

<span data-ttu-id="303b9-137">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="303b9-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="303b9-138">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="303b9-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="303b9-139">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="303b9-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="303b9-140">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="303b9-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="303b9-141">`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="303b9-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="303b9-142">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="303b9-142">Authentication service support</span></span>

<span data-ttu-id="303b9-143">ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="303b9-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="303b9-144">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="303b9-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="303b9-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="303b9-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="303b9-146">メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="303b9-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="303b9-147">アプリの構成に必要な値は、アプリを登録するときに Microsoft アカウントの構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="303b9-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="303b9-148">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="303b9-148">Access token scopes</span></span>

<span data-ttu-id="303b9-149">Webassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではBlazorありません。</span><span class="sxs-lookup"><span data-stu-id="303b9-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="303b9-150">サインインフローの一部としてトークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="303b9-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="303b9-151">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="303b9-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="303b9-152">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="303b9-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="303b9-153">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="303b9-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="303b9-154">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="303b9-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="303b9-155">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="303b9-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="303b9-156">Index ページ</span><span class="sxs-lookup"><span data-stu-id="303b9-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="303b9-157">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="303b9-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="303b9-158">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="303b9-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="303b9-159">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="303b9-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="303b9-160">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="303b9-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="303b9-161">その他の資料</span><span class="sxs-lookup"><span data-stu-id="303b9-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="303b9-162">クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="303b9-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="303b9-163">クイックスタート: web Api を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="303b9-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
