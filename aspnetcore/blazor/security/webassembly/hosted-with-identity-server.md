---
title: ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する
author: guardrex
description: '[IdentityServer](https://identityserver.io/) バックエンドを使用して、Visual Studio 内から認証を用いて新しい Blazor ホスト型アプリを作成するには'
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: cce6b6b1ec144e362415fe34645aef567269c873
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402209"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>ASP.NET Core Blazor WebAssembly でホストされているアプリを Identity Server でセキュリティ保護する

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)

この記事では、ユーザーと API 呼び出しの認証に [IdentityServer](https://identityserver.io/) を使用する、Blazor でホストされたアプリを新しく作成する方法について説明します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio:

1. 新しい **Blazor WebAssembly** アプリを作成します。 詳細については、「<xref:blazor/get-started>」を参照してください。
1. **[新しい Blazor アプリを作成します]** ダイアログで、 **[認証]** セクションの **[変更]** を選択します。
1. **[個人のユーザー アカウント]** を選択し、 **[OK]** を選択します。
1. **[詳細設定]** セクションで、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。
1. **[作成]** ボタンを選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

コマンド シェルでアプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。 また、フォルダー名は、プロジェクトの名前の一部となります。

---

## <a name="server-app-configuration"></a>サーバー アプリの構成

次のセクションでは、認証のサポートが含まれる場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

`Startup` クラスには次のものが追加されます。

* `Startup.ConfigureServices`の場合:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer 上に既定の ASP.NET Core 規則を設定する <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドが追加された IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer によって生成された JWT トークンを検証するようにアプリを構成する <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドが追加された Authentication。

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`の場合:

  * IdentityServer のミドルウェアでは、Open ID Connect (OIDC) のエンドポイントが公開されます。

    ```csharp
    app.UseIdentityServer();
    ```

  * 認証ミドルウェアでは、要求の資格情報の検証と、要求コンテキストでのユーザーの設定が行われます。

    ```csharp
    app.UseAuthentication();
    ```

  * 承認ミドルウェアにより、承認機能が有効になります。

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパー メソッドでは、ASP.NET Core シナリオ対応に [IdentityServer](https://identityserver.io/) が構成されます。 IdentityServer は、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。 IdentityServer を使用すると、ほとんどの一般的なシナリオには必要のない複雑さが発生します。 そのため、使用開始時に適切であると考えられる一連の規則と構成オプションが用意されています。 認証のニーズが変わったら、IdentityServer のあらゆる機能を利用し、アプリの要件に合わせて認証をカスタマイズできます。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパー メソッドでは、アプリに対するポリシー スキームが既定の認証ハンドラーとして構成されます。 そのポリシーは、Identity の URL 空間 `/Identity` のサブパスにルーティングされたすべての要求を Identity で処理できるように構成されています。 それ以外のすべての要求は、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> で処理されます。 さらに、このメソッドでは次のことが行われます。

* 既定のスコープ `{APPLICATION NAME}API` を使用して、`{APPLICATION NAME}API` API リソースが IdentityServer に登録されます。
* IdentityServer によってアプリに対して発行されたトークンを検証するように、JWT ベアラー トークン ミドルウェアが構成されます。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController` (`Controllers/WeatherForecastController.cs`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がクラスに適用されます。 その属性では、ユーザーはリソースにアクセスするために既定のポリシーに基づいて承認される必要があることが示されています。 既定の承認ポリシーは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> によって設定される既定の認証スキームを使用するように構成されています。 ヘルパー メソッドでは、アプリへの要求に対する既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> が構成されます。

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext` (`Data/ApplicationDbContext.cs`) では、<xref:Microsoft.EntityFrameworkCore.DbContext> により、IdentityServer 用のスキーマを含むように <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> が拡張されます。 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。

データベース スキーマを完全に制御するには、使用可能な Identity <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの 1 つを継承し、<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出すことによって、Identity スキーマを含むようにコンテキストを構成します。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) では、OIDC パラメーターを提供するために、クライアント エンドポイントがプロビジョニングされます。

### <a name="app-settings-files"></a>アプリ設定ファイル

プロジェクト ルートにあるアプリ設定ファイル (`appsettings.json`) の `IdentityServer` セクションには、構成されているクライアントの一覧が記述されてます。 次の例には、1 つのクライアントがあります。 クライアント名はアプリケーション名に対応し、規則によって OAuth の `ClientId` パラメーターにマップされます。 構成対象のアプリの種類は、プロファイルによって示されています。 プロファイルは、サーバーの構成プロセスを簡素化する規則を促進するために、内部的に使用されます。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.Client`)。

## <a name="client-app-configuration"></a>クライアント アプリの構成

### <a name="authentication-package"></a>認証パッケージ

個人のユーザー アカウント (`Individual`) を使用するようにアプリを作成すると、アプリはそのアプリのプロジェクト ファイル内で [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>API の承認のサポート

ユーザーの認証に対するサポートは、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージ内で提供される拡張メソッドによって、サービス コンテナーに接続されます。 このメソッドでは、アプリが既存の承認システムとやり取りするために必要なサービスが設定されます。

```csharp
builder.Services.AddApiAuthorization();
```

既定では、アプリの構成は規則により `_configuration/{client-id}` から読み込まれます。 規則により、アプリのアセンブリ名にはクライアント ID が設定されます。 オプションを指定してオーバーロードを呼び出すことにより、別のエンドポイントを指すようにこの URL を変更できます。

### <a name="imports-file"></a>インポート ファイル

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>インデックス ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay コンポーネント

`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。

* 認証されたユーザーの場合:
  * 現在のユーザー名が表示されます。
  * ASP.NET Core Identity のユーザー プロファイル ページへのリンクが提供されます。
  * アプリからログアウトするためのボタンが用意されます。
* 匿名ユーザーの場合:
  * 登録するオプションが提供されます。
  * ログインするオプションが提供されます。

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

サーバー プロジェクトからアプリを実行します。 Visual Studio を使用しているときは、次のいずれかを行います。

* ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。
* **ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。

## <a name="name-and-role-claim-with-api-authorization"></a>API 認証での名前とロール要求

### <a name="custom-user-factory"></a>カスタム ユーザー ファクトリ

クライアント アプリで、カスタム ユーザー ファクトリを作成します。 Identity Server により、複数のロールが JSON 配列として 1 つの `role` 要求で送信されます。 1 つのロールは、要求内で文字列値として送信されます。 ファクトリにより、ユーザーのロールごとに個別の `role` 要求が作成されます。

`CustomUserFactory.cs`:

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

クライアント アプリでは、`Program.Main` (`Program.cs`) にファクトリを登録します。

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

サーバー アプリでは、Identity ビルダーで <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> を呼び出します。これにより、ロールに関連するサービスが追加されます。

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Identity Server を構成する

次の方法の**いずれか**を使用します。

* [API 承認のオプション](#api-authorization-options)
* [プロファイル サービス](#profile-service)

#### <a name="api-authorization-options"></a>API 承認のオプション

サーバー アプリで次のようにします。

* `name` 要求と `role` 要求を ID トークンとアクセス トークンに挿入するように、Identity Server を構成します。
* JWT トークン ハンドラーでロールの既定のマッピングを禁止します。

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

#### <a name="profile-service"></a>プロファイル サービス

サーバー アプリで、`ProfileService` の実装を作成します。

`ProfileService.cs`:

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

サーバー アプリで、`Startup.ConfigureServices` にプロファイル サービスを登録します。

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>承認メカニズムを使用する

クライアント アプリでは、この時点でコンポーネントの承認方法が機能しています。 コンポーネント内のすべての承認メカニズムで、ロールを使用してユーザーを承認できます。

* [`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)
* [`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)
* [手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)

  複数のロール テストがサポートされています。

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` には、クライアント アプリにおいてユーザーのユーザー名が設定されます。これは通常、サインイン メール アドレスです。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* [Azure App Service にデプロイする](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault から証明書をインポートする (Azure ドキュメント)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
