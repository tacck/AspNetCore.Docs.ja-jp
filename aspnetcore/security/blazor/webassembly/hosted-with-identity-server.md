---
title: Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity
author: guardrex
description: Blazorホスティング[サーバー](https://identityserver.io/)バックエンドを使用する Visual Studio 内から認証を使用して新しいホスト型アプリを作成するには
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776515"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Blazorサーバーを使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する Identity

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

この記事では、 Blazor ユーザーと API 呼び出しを認証する[IdentityServer](https://identityserver.io/)ために、ユーザーを使用する新しいホスト型アプリを作成する方法について説明します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio で次の操作を行います。

1. ** Blazor 新しいアプリを作成し**ます。 詳細については、「<xref:blazor/get-started>」を参照してください。
1. [**新しい Blazor アプリの作成**] ダイアログで、[**認証**] セクションの [**変更**] を選択します。
1. **個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。
1. [**詳細設定**] セクションで [ホストされている**ASP.NET Core** ] チェックボックスをオンにします。
1. **[作成]** ボタンを選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンドシェルでアプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。 フォルダー名もプロジェクトの名前の一部になります。

---

## <a name="server-app-configuration"></a>サーバーアプリの構成

次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

クラスには `Startup` 次の追加機能があります。

* `Startup.ConfigureServices`の場合:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * サーバーには、次のように、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 既定の ASP.NET Core 規則を設定するヘルパーメソッドが追加されています。

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

  * サーバーミドルウェアは Open ID Connect (OIDC) エンドポイントを公開します。

    ```csharp
    app.UseIdentityServer();
    ```

  * 認証ミドルウェアは、要求の資格情報を検証し、要求コンテキストでユーザーを設定する役割を担います。

    ```csharp
    app.UseAuthentication();
    ```

  * 承認ミドルウェアにより、承認機能が有効になります。

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>ヘルパーメソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。 サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。 一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。 そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。 認証を変更する必要がある場合は、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>ヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。 ポリシーは、が URL 空間内のすべてのサブパスにルーティングされたすべての要求を処理できるように構成されてい Identity Identity `/Identity` ます。 は、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 他のすべての要求を処理します。 さらに、このメソッドは次のようになります。

* `{APPLICATION NAME}API`既定のスコープのを使用して、API リソースをに登録 `{APPLICATION NAME}API` します。
* アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController`(*Controllers/WeatherForecastController*) で、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。 属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。 既定の承認ポリシーは、によって設定される既定の認証スキームを使用するように構成されてい <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ます。 ヘルパーメソッドは、 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> アプリに対する要求の既定のハンドラーとしてを構成します。

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`(*Data/ApplicationDbContext .cs*) では、を拡張して、をに <xref:Microsoft.EntityFrameworkCore.DbContext> 指定し <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> ます。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。

データベーススキーマを完全に制御するには、使用可能なクラスの1つを継承 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> し、メソッドでを呼び出してスキーマを含めるようにコンテキストを構成し Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> ます。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController`(*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。

### <a name="app-settings-files"></a>アプリ設定ファイル

プロジェクトルートのアプリ設定ファイル (*appsettings.js*) では、 `IdentityServer` 構成されているクライアントの一覧がセクションに示されます。 次の例には、1つのクライアントがあります。 クライアント名はアプリケーション名に対応し、OAuth パラメーターに規約によってマップされ `ClientId` ます。 プロファイルは、構成されているアプリの種類を示します。 プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 (など `BlazorSample.Client` ) です。

## <a name="client-app-configuration"></a>クライアントアプリの構成

### <a name="authentication-package"></a>認証パッケージ

個々のユーザーアカウント () を使用するようにアプリを作成すると、アプリは、アプリ `Individual` のプロジェクトファイルで[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>API 承認のサポート

ユーザーを認証するためのサポートは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージ内に用意されている拡張メソッドによってサービスコンテナーに接続されます。 このメソッドは、既存の承認システムと対話するために、アプリが必要とするサービスを設定します。

```csharp
builder.Services.AddApiAuthorization();
```

既定では、アプリの構成はの規則によって読み込まれ `_configuration/{client-id}` ます。 規則により、クライアント ID はアプリのアセンブリ名に設定されます。 この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。

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

サーバープロジェクトからアプリを実行します。 Visual Studio を使用する場合は、次のいずれかの方法で行います。

* ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。
* **ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

## <a name="name-and-role-claim-with-api-authorization"></a>API 認証を使用した名前とロール要求

### <a name="custom-user-factory"></a>カスタムユーザーファクトリ

クライアントアプリで、カスタムユーザーファクトリを作成します。 Identityサーバーは、1つの要求で複数のロールを JSON 配列として送信 `role` します。 1つのロールが、要求の文字列値として送信されます。 ファクトリは、 `role` ユーザーのロールごとに個別の要求を作成します。

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

クライアントアプリで、ファクトリを `Program.Main` (*Program.cs*) に登録します。

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

サーバーアプリで、ビルダーでを呼び出し <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity ます。これにより、ロールに関連するサービスが追加されます。

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>サーバーの構成 Identity

次の方法の**いずれか**を使用します。

* [API 承認オプション](#api-authorization-options)
* [プロファイルサービス](#profile-service)

#### <a name="api-authorization-options"></a>API 承認オプション

サーバーアプリで次のようにします。

* Identity `name` および `role` 要求を ID トークンとアクセストークンに配置するようにサーバーを構成します。
* JWT トークンハンドラーでロールの既定のマッピングを使用しないようにします。

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>プロファイルサービス

サーバーアプリで、実装を作成 `ProfileService` します。

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

サーバーアプリで、次のようにプロファイルサービスを登録し `Startup.ConfigureServices` ます。

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>認証メカニズムを使用する

クライアントアプリでは、この時点でコンポーネントの承認方法が機能します。 コンポーネント内のすべての承認メカニズムは、ロールを使用してユーザーを承認できます。

* [Authorizeview コンポーネント](xref:security/blazor/index#authorizeview-component)(例: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` attribute ディレクティブ](xref:security/blazor/index#authorize-attribute)( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (例: `@attribute [Authorize(Roles = "admin")]` )
* [手続き型ロジック](xref:security/blazor/index#procedural-logic)(例: `if (user.IsInRole("admin")) { ... }` )

  複数のロールテストがサポートされています。

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`は、ユーザーのユーザー名を使用してクライアントアプリに設定されます。これは通常、サインイン電子メールアドレスです。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* [Azure App Service への配置](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault から証明書をインポートする (Azure ドキュメント)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
