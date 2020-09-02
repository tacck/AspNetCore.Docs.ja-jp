---
title: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: 認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 795709853941f35b1645f72d6865fe1ebf935112
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712364"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="065d8-103">認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="065d8-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="065d8-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="065d8-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="065d8-105">*Azure Active Directory (AAD) および Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従わず、この目次ノードの AAD および AAD B2C のトピックを参照してください。*</span><span class="sxs-lookup"><span data-stu-id="065d8-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="065d8-106">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ライブラリを使用する [スタンドアロン Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成するには、選択したツールのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="065d8-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="065d8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="065d8-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="065d8-108">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="065d8-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="065d8-109">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor WebAssembly アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="065d8-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="065d8-110">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、 **[アプリ内のストア ユーザー アカウント]** オプションを使用して、 **[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="065d8-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="065d8-111">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="065d8-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="065d8-112">空のフォルダーに、認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="065d8-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="065d8-113">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用してアプリ内にユーザーを格納するには、`-au|--auth` オプションを使用して `Individual` 認証メカニズムを指定します。</span><span class="sxs-lookup"><span data-stu-id="065d8-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="065d8-114">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="065d8-114">Placeholder</span></span>  | <span data-ttu-id="065d8-115">例</span><span class="sxs-lookup"><span data-stu-id="065d8-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="065d8-116">`-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="065d8-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="065d8-117">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="065d8-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="065d8-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="065d8-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="065d8-119">認証メカニズムを使用して新しい Blazor WebAssembly プロジェクトを作成するには:</span><span class="sxs-lookup"><span data-stu-id="065d8-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="065d8-120">**[新しい Blazor WebAssembly アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="065d8-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="065d8-121">ASP.NET Core の [Identity](xref:security/authentication/identity) を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="065d8-122">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="065d8-122">Authentication package</span></span>

<span data-ttu-id="065d8-123">個人のユーザー アカウントを使用するようにアプリを作成すると、そのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="065d8-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="065d8-124">このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="065d8-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="065d8-125">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="065d8-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="065d8-126">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="065d8-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="065d8-127">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="065d8-127">Authentication service support</span></span>

<span data-ttu-id="065d8-128">ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="065d8-129">このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="065d8-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="065d8-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="065d8-131">構成は `wwwroot/appsettings.json` ファイルによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="065d8-132">スタンドアロン アプリの認証サポートは、OpenID Connect (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="065d8-133"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> メソッドでは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="065d8-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="065d8-134">アプリの構成に必要な値は、OIDC に準拠する IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="065d8-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="065d8-135">アプリを登録するときに値を取得します。これは通常、オンライン ポータルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="065d8-136">アクセス トークン スコープ</span><span class="sxs-lookup"><span data-stu-id="065d8-136">Access token scopes</span></span>

<span data-ttu-id="065d8-137">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="065d8-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="065d8-138">サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> の既定のトークン スコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="065d8-138">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="065d8-139">詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="065d8-139">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="065d8-140">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="065d8-140">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="065d8-141">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="065d8-141">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="065d8-142">インポート ファイル</span><span class="sxs-lookup"><span data-stu-id="065d8-142">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="065d8-143">インデックス ページ</span><span class="sxs-lookup"><span data-stu-id="065d8-143">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="065d8-144">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="065d8-144">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="065d8-145">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="065d8-145">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="065d8-146">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="065d8-146">LoginDisplay component</span></span>

<span data-ttu-id="065d8-147">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-147">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="065d8-148">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="065d8-148">For authenticated users:</span></span>
  * <span data-ttu-id="065d8-149">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-149">Displays the current username.</span></span>
  * <span data-ttu-id="065d8-150">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-150">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="065d8-151">匿名ユーザーの場合は、ログインするオプションが用意されます。</span><span class="sxs-lookup"><span data-stu-id="065d8-151">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="065d8-152">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="065d8-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="065d8-153">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="065d8-153">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="065d8-154">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="065d8-154">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
