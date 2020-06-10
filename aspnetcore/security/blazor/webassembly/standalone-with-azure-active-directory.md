---
title: BlazorAzure Active Directory を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b85129d8b56f2106636b47534630f8139e100ae9
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851212"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="3caf9-102">BlazorAzure Active Directory を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="3caf9-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="3caf9-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3caf9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3caf9-104">Blazor認証に[AZURE ACTIVE DIRECTORY (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する webassembly アプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3caf9-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="3caf9-105">[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="3caf9-106">Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3caf9-107">アプリの**名前**を指定します ( \*\* Blazor スタンドアロン AAD\*\*など)。</span><span class="sxs-lookup"><span data-stu-id="3caf9-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="3caf9-108">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="3caf9-109">この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="3caf9-110">[**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="3caf9-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="3caf9-111">Kestrel で実行されているアプリの既定のポートは5001です。</span><span class="sxs-lookup"><span data-stu-id="3caf9-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="3caf9-112">アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="3caf9-113">IIS Express の場合、アプリのランダムに生成されたポートは、[**デバッグ**] パネルのアプリの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="3caf9-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="3caf9-114">この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。</span><span class="sxs-lookup"><span data-stu-id="3caf9-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="3caf9-115">このトピックの後半では、リダイレクト URI を更新するようにユーザーに IIS Express 通知するために、このトピックの後半でコメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="3caf9-116">[ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="3caf9-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3caf9-117">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-117">Select **Register**.</span></span>

<span data-ttu-id="3caf9-118">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-118">Record the following information:</span></span>

* <span data-ttu-id="3caf9-119">アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="3caf9-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3caf9-120">ディレクトリ ID (テナント ID) (など `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="3caf9-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="3caf9-121">[**認証**  >  **プラットフォーム構成**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="3caf9-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3caf9-122">の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3caf9-123">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3caf9-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3caf9-124">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3caf9-125">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-125">Select the **Save** button.</span></span>

<span data-ttu-id="3caf9-126">アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-126">Create the app.</span></span> <span data-ttu-id="3caf9-127">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3caf9-128">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3caf9-129">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="3caf9-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="3caf9-130">Azure portal では、アプリの**認証**  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="3caf9-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="3caf9-131">アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは [**デバッグ**] パネルのアプリの [プロパティ] にあります。</span><span class="sxs-lookup"><span data-stu-id="3caf9-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="3caf9-132">ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="3caf9-133">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="3caf9-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="3caf9-134">AAD ユーザーアカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="3caf9-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="3caf9-135">Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="3caf9-136">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="3caf9-136">For more information, see:</span></span>
  * [<span data-ttu-id="3caf9-137">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="3caf9-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="3caf9-138">[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。</span><span class="sxs-lookup"><span data-stu-id="3caf9-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3caf9-139">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="3caf9-139">Authentication package</span></span>

<span data-ttu-id="3caf9-140">職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)([Microsoft. authentication. Webas. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3caf9-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="3caf9-141">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3caf9-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3caf9-142">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="3caf9-143">この[Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)パッケージは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージを推移的にアプリに追加します。このパッケージは、アプリケーションに対して推移的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3caf9-144">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="3caf9-144">Authentication service support</span></span>

<span data-ttu-id="3caf9-145">ユーザー認証のサポートは、サービスコンテナーに登録され <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> ている拡張メソッドを[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="3caf9-146">このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3caf9-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3caf9-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="3caf9-148">メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3caf9-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3caf9-149">アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="3caf9-150">構成は*wwwroot/appsettings*ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="3caf9-151">例:</span><span class="sxs-lookup"><span data-stu-id="3caf9-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="3caf9-152">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="3caf9-152">Access token scopes</span></span>

<span data-ttu-id="3caf9-153">BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="3caf9-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3caf9-154">サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="3caf9-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="3caf9-155">詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3caf9-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="3caf9-156">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="3caf9-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="3caf9-157">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="3caf9-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="3caf9-158">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="3caf9-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="3caf9-159">Index ページ</span><span class="sxs-lookup"><span data-stu-id="3caf9-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="3caf9-160">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="3caf9-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3caf9-161">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3caf9-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3caf9-162">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3caf9-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3caf9-163">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3caf9-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3caf9-164">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3caf9-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="3caf9-165">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="3caf9-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="3caf9-166">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="3caf9-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
