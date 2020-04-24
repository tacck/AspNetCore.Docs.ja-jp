---
title: 認証ライブラリをBlazor使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110950"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="9ce58-102">認証ライブラリをBlazor使用して ASP.NET Core webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="9ce58-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="9ce58-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9ce58-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="9ce58-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="9ce58-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="9ce58-106">*Azure Active Directory (AAD) と Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従ってください。この目次ノードの AAD と AAD B2C のトピックを参照してください。*</span><span class="sxs-lookup"><span data-stu-id="9ce58-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="9ce58-107">ライブラリを使用Blazor `Microsoft.AspNetCore.Components.WebAssembly.Authentication`する webassembly アプリを作成するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="9ce58-108">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9ce58-109">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="9ce58-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="9ce58-110">Visual Studio で、 [webassembly Blazorを作成](xref:blazor/get-started)します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="9ce58-111">[**ユーザーアカウントをアプリ内に格納**する] オプションを使用して、**認証**を**個々のユーザーアカウント**に設定します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="9ce58-112">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="9ce58-112">Authentication package</span></span>

<span data-ttu-id="9ce58-113">個々のユーザーアカウントを使用するようにアプリを作成すると、アプリはアプリのプロジェクト`Microsoft.AspNetCore.Components.WebAssembly.Authentication`ファイルでパッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9ce58-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="9ce58-114">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9ce58-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9ce58-115">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="9ce58-116">前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="9ce58-117">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="9ce58-117">Authentication service support</span></span>

<span data-ttu-id="9ce58-118">ユーザー認証のサポートは、 `AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="9ce58-119">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9ce58-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9ce58-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="9ce58-121">スタンドアロンアプリの認証サポートは、Open ID Connect (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="9ce58-122">メソッド`AddOidcAuthentication`は、oidc を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="9ce58-123">アプリの構成に必要な値は、OIDC に準拠している IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="9ce58-124">アプリを登録するときに値を取得します。これは通常、オンラインポータルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9ce58-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="9ce58-125">アクセストークンスコープ</span><span class="sxs-lookup"><span data-stu-id="9ce58-125">Access token scopes</span></span>

<span data-ttu-id="9ce58-126">Webassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではBlazorありません。</span><span class="sxs-lookup"><span data-stu-id="9ce58-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="9ce58-127">サインインフローの一部としてトークンをプロビジョニングするには、の既定のトークンスコープにスコープを追加`OidcProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="9ce58-128">Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="9ce58-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="9ce58-129">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="9ce58-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="9ce58-130">スキームとホストなしでスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="9ce58-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="9ce58-131">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9ce58-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="9ce58-132">ファイルのインポート</span><span class="sxs-lookup"><span data-stu-id="9ce58-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="9ce58-133">Index ページ</span><span class="sxs-lookup"><span data-stu-id="9ce58-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="9ce58-134">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="9ce58-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="9ce58-135">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9ce58-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="9ce58-136">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9ce58-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="9ce58-137">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9ce58-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9ce58-138">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9ce58-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 