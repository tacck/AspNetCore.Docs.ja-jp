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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: be87257c5f901e9b3d1ba6a8d7c6b811419c433f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402196"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>認証ライブラリを使用して、ASP.NET Core Blazor WebAssembly スタンドアロン アプリをセキュリティで保護する

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)

*Azure Active Directory (AAD) および Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従わず、この目次ノードの AAD および AAD B2C のトピックを参照してください。*

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ライブラリを使用する Blazor WebAssembly スタンドアロン アプリを作成するには、コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual
```

出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。 また、フォルダー名は、プロジェクトの名前の一部となります。

Visual Studio で、[Blazor WebAssembly アプリを作成します](xref:blazor/get-started)。 **[アプリ内のストア ユーザー アカウント]** オプションで、 **[認証]** を **[個人のユーザー アカウント]** に設定します。

## <a name="authentication-package"></a>認証パッケージ

個人のユーザー アカウントを使用するようにアプリを作成すると、そのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。 このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

構成は `wwwroot/appsettings.json` ファイルによって提供されます。

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

スタンドアロン アプリの認証サポートは、Open ID Connect (OIDC) を使用して提供されます。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> メソッドでは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。 アプリの構成に必要な値は、OIDC に準拠する IP から取得できます。 アプリを登録するときに値を取得します。これは通常、オンライン ポータルで実行されます。

## <a name="access-token-scopes"></a>アクセス トークン スコープ

Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセス トークンを要求するようにアプリが自動的に構成されるわけではありません。 サインイン フローの一部としてアクセス トークンをプロビジョニングするには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> の既定のトークン スコープにスコープを追加します。

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。

* [追加のアクセス トークンを要求する](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [送信要求にトークンを添付する](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>インポート ファイル

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>インデックス ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay コンポーネント

`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。

* 認証されたユーザーの場合:
  * 現在のユーザー名が表示されます。
  * アプリからログアウトするためのボタンが用意されます。
* 匿名ユーザーの場合は、ログインするオプションが用意されます。

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

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/security/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
