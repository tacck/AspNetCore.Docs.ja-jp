---
title: Id サーバーをBlazor使用して、ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ホスティングサーバーバックエンドBlazorを使用する Visual Studio 内から認証を使用し[IdentityServer](https://identityserver.io/)て新しいホスト型アプリを作成するには
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110949"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Id サーバーをBlazor使用して、ASP.NET Core webasのホスト型アプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。 このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。

ユーザーと API 呼び出しBlazorを認証するためにユーザーを[使用する](https://identityserver.io/)、Visual Studio で新しいホスト型アプリを作成するには、次のようにします。

1. Visual Studio を使用して、 ** Blazor新しいアプリを作成し**ます。 詳細については、「<xref:blazor/get-started>」を参照してください。
1. [**新しいBlazorアプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。
1. **個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。
1. [**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。
1. **[作成]** ボタンを選択します。

コマンドシェルでアプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。 フォルダー名もプロジェクトの名前の一部になります。

## <a name="server-app-configuration"></a>サーバーアプリの構成

次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

クラス`Startup`には、次の追加機能があります。

* `Startup.ConfigureServices`の場合:

  * ID:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * サーバーには、次<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>のように、既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドがあります。

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 次のように<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>追加のヘルパーメソッドを使用して認証します。これにより、アプリは、サービスによって生成された JWT トークンを検証します。

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

### <a name="addapiauthorization"></a>AddApiAuthorization

ヘルパー <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>メソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。 サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。 一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。 そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。 認証を変更する必要が生じた場合でも、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

ヘルパー <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>メソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。 このポリシーは、id URL 空間`/Identity`内のサブパスにルーティングされたすべての要求を id が処理できるように構成されています。 は<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 、他のすべての要求を処理します。 さらに、このメソッドは次のようになります。

* 既定の`{APPLICATION NAME}API`スコープの`{APPLICATION NAME}API`を使用して、API リソースをに登録します。
* アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController` (*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性がクラスに適用されます。 属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。 既定の承認ポリシーは、既定の認証スキームを使用するように構成され<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ます。これは、前に説明したポリシースキームによって設定されます。 ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>アプリに対する要求の既定のハンドラーとしてを構成します。

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` (*Data/applicationdbcontext .cs*) では、id には<xref:Microsoft.EntityFrameworkCore.DbContext> 、ユーザーのスキーマを含むように拡張<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>された例外と同じが使用されます。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。

データベーススキーマを完全に制御するには、使用可能な Id <xref:Microsoft.EntityFrameworkCore.DbContext>クラスの1つを継承し、 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating`メソッドでを呼び出して、id スキーマを含めるようにコンテキストを構成します。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController` (*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。

### <a name="app-settings-files"></a>アプリ設定ファイル

プロジェクトルートのアプリ設定ファイル (*appsettings. json*) で、構成され`IdentityServer`ているクライアントの一覧を説明するセクションです。 次の例には、1つのクライアントがあります。 クライアント名はアプリケーション名に対応し、OAuth `ClientId`パラメーターに規約によってマップされます。 プロファイルは、構成されているアプリの種類を示します。 プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

開発環境のアプリ設定ファイル (appsettings) で、*appsettings.Development.json*この`IdentityServer`セクションでは、プロジェクトルートでの json の署名に使用されるキーについて説明します。 <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>クライアントアプリの構成

### <a name="authentication-package"></a>認証パッケージ

個々のユーザーアカウント (`Individual`) を使用するようにアプリを作成すると、アプリはアプリのプロジェクト`Microsoft.AspNetCore.Components.WebAssembly.Authentication`ファイルでパッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。

### <a name="api-authorization-support"></a>API 承認のサポート

ユーザー認証のサポートは、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージ内で提供される拡張メソッドによってサービスコンテナーに接続されます。 このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。

```csharp
builder.Services.AddApiAuthorization();
```

既定では、から`_configuration/{client-id}`の規則によって、アプリの構成が読み込まれます。 規則により、クライアント ID はアプリのアセンブリ名に設定されます。 この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。

### <a name="imports-file"></a>ファイルのインポート

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay コンポーネント

コンポーネント (*shared/Logindisplay*) は`MainLayout` 、コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。 `LoginDisplay`

* 認証されたユーザーの場合:
  * 現在のユーザー名を表示します。
  * ASP.NET Core Id のユーザープロファイルページへのリンクを提供します。
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

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/blazor/webassembly/additional-scenarios>
