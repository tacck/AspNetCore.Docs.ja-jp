---
title: Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity
author: guardrex
description: Blazorホスティング[サーバー](https://identityserver.io/)バックエンドを使用する Visual Studio 内から認証を使用して新しいホスト型アプリを作成するには
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153631"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazorユーザーと API 呼び出しを認証するためにユーザーを使用[する、](https://identityserver.io/) Visual Studio で新しいホスト型アプリを作成するには、次のようにします。

1. Visual Studio を使用して、 ** Blazor 新しいアプリを作成し**ます。 詳細については、「<xref:blazor/get-started>」を参照してください。
1. [**新しい Blazor アプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。
1. **個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。
1. [**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。
1. **[作成]** ボタンを選択します。

コマンドシェルでアプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。 フォルダー名もプロジェクトの名前の一部になります。

## <a name="server-app-configuration"></a>サーバーアプリの構成

次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

クラスには `Startup` 、次の追加機能があります。

* `Startup.ConfigureServices`の場合:

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * サーバーには、次のように、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 次のように追加のヘルパーメソッドを使用して認証し <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。これにより、アプリは、サービスによって生成された JWT トークンを検証します。

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`の場合:

  * 要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect (OIDC) エンドポイントを公開するサーバーミドルウェア:

    ```csharp
    app.UseIdentityServer();
    ```

  * 認証と承認のミドルウェア:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>ヘルパーメソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。 サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。 一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。 そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。 認証を変更する必要が生じた場合でも、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。 ポリシーは、が URL 空間内のすべてのサブパスにルーティングされたすべての要求を処理できるように構成されてい Identity Identity `/Identity` ます。 は、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 他のすべての要求を処理します。 さらに、このメソッドは次のようになります。

* `{APPLICATION NAME}API`既定のスコープのを使用して、API リソースをに登録 `{APPLICATION NAME}API` します。
* アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController`(*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。 属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。 既定の承認ポリシーは、既定の認証スキームを使用するように構成されます。これは、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 前に説明したポリシースキームによって設定されます。 ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> アプリに対する要求の既定のハンドラーとしてを構成します。

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`(*Data/ApplicationDbContext .cs*) では、でも同じもの <xref:Microsoft.EntityFrameworkCore.DbContext> が使用され Identity ます。ただし、この例外は、を使用して、の <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> スキーマを追加します。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。

データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` ます。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController`(*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。

### <a name="app-settings-files"></a>アプリ設定ファイル

プロジェクトルートのアプリ設定ファイル (*appsettings. json*) で、 `IdentityServer` 構成されているクライアントの一覧を説明するセクションです。 次の例には、1つのクライアントがあります。 クライアント名はアプリケーション名に対応し、OAuth パラメーターに規約によってマップされ `ClientId` ます。 プロファイルは、構成されているアプリの種類を示します。 プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>クライアントアプリの構成

### <a name="authentication-package"></a>認証パッケージ

個々のユーザーアカウント () を使用するようにアプリを作成すると、アプリはアプリの `Individual` プロジェクトファイルでパッケージのパッケージ参照を自動的に受け取り `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ます。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

`{VERSION}`前のパッケージ参照のを、この記事に示されているパッケージのバージョンに置き換え `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> ます。

### <a name="api-authorization-support"></a>API 承認のサポート

ユーザー認証のサポートは、パッケージ内で提供される拡張メソッドによってサービスコンテナーに接続され `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ます。 このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。

```csharp
builder.Services.AddApiAuthorization();
```

既定では、からの規則によって、アプリの構成が読み込ま `_configuration/{client-id}` れます。 規則により、クライアント ID はアプリのアセンブリ名に設定されます。 この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。

### <a name="imports-file"></a>ファイルのインポート

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay コンポーネント

コンポーネント `LoginDisplay` (*Shared/logindisplay*) は、 `MainLayout` コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。

* 認証されたユーザーの場合:
  * 現在のユーザー名を表示します。
  * ASP.NET Core の [ユーザープロファイル] ページへのリンクを提供 Identity します。
  * アプリからログアウトするためのボタンが用意されています。
* 匿名ユーザーの場合:
  * 登録するオプションが用意されています。
  * ログインするオプションが用意されています。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData コンポーネント

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>アプリを実行する

サーバープロジェクトからアプリを実行します。 Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* <xref:security/blazor/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
