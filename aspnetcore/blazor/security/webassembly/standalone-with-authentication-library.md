---
title: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 5a05543c77f1ebaebadc27236aa8f7634e84f1fd
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243416"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="48685-102">認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="48685-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="48685-103">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48685-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="48685-104">*Azure Active Directory (AAD) および Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従わず、この目次ノードの AAD および AAD B2C のトピックを参照してください。*</span><span class="sxs-lookup"><span data-stu-id="48685-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="48685-105">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ライブラリを使用する Blazor WebAssembly スタンドアロン アプリを作成するには、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="48685-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="48685-106">出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。</span><span class="sxs-lookup"><span data-stu-id="48685-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="48685-107">また、フォルダー名は、プロジェクトの名前の一部となります。</span><span class="sxs-lookup"><span data-stu-id="48685-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="48685-108">Visual Studio で、[Blazor WebAssembly アプリを作成します](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="48685-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="48685-109">**[アプリ内のストア ユーザー アカウント]** オプションで、 **[認証]** を **[個人のユーザー アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="48685-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="48685-110">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="48685-110">Authentication package</span></span>

<span data-ttu-id="48685-111">個人のユーザー アカウントを使用するようにアプリを作成すると、そのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="48685-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="48685-112">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="48685-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="48685-113">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="48685-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="48685-114">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="48685-114">Authentication service support</span></span>

<span data-ttu-id="48685-115">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="48685-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="48685-116">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="48685-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="48685-117">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="48685-117">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="48685-118">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="48685-118">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="48685-119">スタンドアロン アプリの認証サポートは、Open ID Connect (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="48685-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="48685-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> メソッドでは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="48685-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="48685-121">アプリの構成に必要な値は、OIDC に準拠する IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="48685-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="48685-122">アプリを登録するときに値を取得します。これは通常、オンライン ポータルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="48685-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="48685-123">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="48685-123">Access token scopes</span></span>

<span data-ttu-id="48685-124">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="48685-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="48685-125">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> の既定のトークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="48685-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="48685-126">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="48685-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="48685-127">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="48685-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="48685-128">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="48685-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="48685-129">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="48685-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="48685-130">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="48685-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="48685-131">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="48685-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="48685-132">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="48685-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="48685-133">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="48685-133">LoginDisplay component</span></span>

<span data-ttu-id="48685-134">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="48685-134">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="48685-135">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="48685-135">For authenticated users:</span></span>
  * <span data-ttu-id="48685-136">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="48685-136">Displays the current username.</span></span>
  * <span data-ttu-id="48685-137">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="48685-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="48685-138">匿名ユーザーの場合は、ログインするオプションが用意されます。</span><span class="sxs-lookup"><span data-stu-id="48685-138">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="48685-139">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="48685-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="48685-140">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="48685-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="48685-141">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="48685-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
