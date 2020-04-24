---
title: Azure Active Directory B2C を使用Blazorして ASP.NET Core のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111163"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="008ec-102">Azure Active Directory B2C を使用Blazorして ASP.NET Core のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="008ec-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="008ec-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="008ec-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="008ec-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="008ec-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="008ec-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="008ec-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="008ec-106">認証にBlazor [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する webassembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="008ec-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="008ec-107">テナントを作成し、Azure Portal で web アプリを登録するには、次のトピックのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="008ec-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="008ec-108">[AAD B2C のテナント](/azure/active-directory-b2c/tutorial-create-tenant) &ndash;レコードを作成して、次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="008ec-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="008ec-109">1 \。</span><span class="sxs-lookup"><span data-stu-id="008ec-109">1\.</span></span> <span data-ttu-id="008ec-110">AAD B2C インスタンス (たとえば`https://contoso.b2clogin.com/`、末尾のスラッシュを含む)</span><span class="sxs-lookup"><span data-stu-id="008ec-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="008ec-111">2 \。</span><span class="sxs-lookup"><span data-stu-id="008ec-111">2\.</span></span> <span data-ttu-id="008ec-112">テナントドメインの AAD B2C (例`contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="008ec-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="008ec-113">&ndash; [Web アプリケーションを登録する](/azure/active-directory-b2c/tutorial-register-applications)アプリの登録時に、次の選択を行います。</span><span class="sxs-lookup"><span data-stu-id="008ec-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="008ec-114">1 \。</span><span class="sxs-lookup"><span data-stu-id="008ec-114">1\.</span></span> <span data-ttu-id="008ec-115">[ **Web アプリ/WEB API** **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="008ec-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="008ec-116">2 \。</span><span class="sxs-lookup"><span data-stu-id="008ec-116">2\.</span></span> <span data-ttu-id="008ec-117">[**暗黙的フローを許可**する **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="008ec-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="008ec-118">3 \。</span><span class="sxs-lookup"><span data-stu-id="008ec-118">3\.</span></span> <span data-ttu-id="008ec-119">の`https://localhost:5001/authentication/login-callback`**応答 URL**を追加します。</span><span class="sxs-lookup"><span data-stu-id="008ec-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="008ec-120">アプリケーション ID (クライアント ID) を記録します (たとえば`11111111-1111-1111-1111-111111111111`、)。</span><span class="sxs-lookup"><span data-stu-id="008ec-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="008ec-121">[ユーザーフロー](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash;の作成サインアップとサインインのユーザーフローを作成します。</span><span class="sxs-lookup"><span data-stu-id="008ec-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="008ec-122">少なくとも、**アプリケーション要求** > の**表示名**ユーザー属性を選択して`context.User.Identity.Name` 、 `LoginDisplay`コンポーネントにを設定します (*Shared/logindisplay. razor*)。</span><span class="sxs-lookup"><span data-stu-id="008ec-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="008ec-123">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (例: `B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="008ec-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="008ec-124">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="008ec-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="008ec-125">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="008ec-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="008ec-126">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="008ec-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="008ec-127">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="008ec-127">Authentication package</span></span>

<span data-ttu-id="008ec-128">個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="008ec-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="008ec-129">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="008ec-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="008ec-130">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="008ec-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="008ec-131">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="008ec-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="008ec-132">`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="008ec-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="008ec-133">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="008ec-133">Authentication service support</span></span>

<span data-ttu-id="008ec-134">ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="008ec-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="008ec-135">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="008ec-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="008ec-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="008ec-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="008ec-137">メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="008ec-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="008ec-138">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="008ec-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="008ec-139">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="008ec-139">Access token scopes</span></span>

<span data-ttu-id="008ec-140">Webassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではBlazorありません。</span><span class="sxs-lookup"><span data-stu-id="008ec-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="008ec-141">サインインフローの一部としてトークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="008ec-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="008ec-142">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="008ec-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="008ec-143">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="008ec-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="008ec-144">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="008ec-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="008ec-145">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="008ec-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="008ec-146">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="008ec-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="008ec-147">Index ページ</span><span class="sxs-lookup"><span data-stu-id="008ec-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="008ec-148">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="008ec-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="008ec-149">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="008ec-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="008ec-150">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="008ec-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="008ec-151">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="008ec-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="008ec-152">その他の資料</span><span class="sxs-lookup"><span data-stu-id="008ec-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="008ec-153">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="008ec-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="008ec-154">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="008ec-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
