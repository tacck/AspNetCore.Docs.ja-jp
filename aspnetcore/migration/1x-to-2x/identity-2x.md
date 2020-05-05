---
title: 認証とIdentity ASP.NET Core 2.0 への移行
author: scottaddie
description: この記事では ASP.NET Core 1.x 認証とIdentity ASP.NET Core 2.0 に移行するための最も一般的な手順について説明します。
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: e828446716d88d92aeb587874421a5751dcb6de0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769502"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>認証とIdentity ASP.NET Core 2.0 への移行

[Scott Addie](https://github.com/scottaddie)および[hao Kung](https://github.com/HaoK)

ASP.NET Core 2.0 には、認証のための[Identity](xref:security/authentication/identity)新しいモデルが用意されており、サービスを使用した構成が簡単になります。 認証を使用するかIdentity 、次に示すように、新しいモデルを使用するように更新することができる ASP.NET Core 1.x アプリケーション。

## <a name="update-namespaces"></a>名前空間の更新

1.x では`IdentityRole` `IdentityUser` `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 、やなどのクラスは名前空間にありました。

2.0 では、 <xref:Microsoft.AspNetCore.Identity>このようなクラスのいくつかの新しいホームに名前空間が追加されました。 既定Identityのコードでは、影響を`ApplicationUser`受ける`Startup`クラスにはおよびが含まれます。 ステートメントを`using`調整して、影響を受ける参照を解決します。

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>認証ミドルウェアとサービス

1.x プロジェクトでは、認証はミドルウェアを介して構成されます。 ミドルウェアメソッドは、サポートする各認証スキームに対して呼び出されます。

次の 1. x の例では、 Identity *Startup.cs*で Facebook 認証を構成します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

2.0 プロジェクトでは、認証はサービスを介して構成されます。 各認証スキームは、 `ConfigureServices` *Startup.cs*のメソッドに登録されます。 `UseIdentity`メソッドはに`UseAuthentication`置き換えられます。

次の2.0 の例では、 Identity *Startup.cs*ので Facebook 認証を構成します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

メソッド`UseAuthentication`は、認証ミドルウェアコンポーネントを1つ追加します。これは、自動認証とリモート認証要求の処理を担当します。 個々のミドルウェアコンポーネントはすべて、単一の共通ミドルウェアコンポーネントに置き換えられます。

次に、各主要な認証スキームの2.0 移行手順を示します。

### <a name="cookie-based-authentication"></a>Cookie ベースの認証

次の2つのオプションのいずれかを選択し、 *Startup.cs*で必要な変更を行います。

1. Cookie を使用するIdentity
    - メソッド内のをに`UseIdentity` `UseAuthentication`置き換えます。 `Configure`

        ```csharp
        app.UseAuthentication();
        ```

    - `ConfigureServices`メソッドで`AddIdentity`メソッドを呼び出して、cookie 認証サービスを追加します。
    - 必要に応じて`ConfigureApplicationCookie` 、 `ConfigureExternalCookie` `ConfigureServices`メソッドでメソッドまたはメソッドをIdentity呼び出して、cookie の設定を調整します。

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Cookie を使用しないIdentity
    - メソッドの`UseCookieAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

        ```csharp
        app.UseAuthentication();
        ```

    - メソッドで`AddAuthentication`メソッド`AddCookie`とメソッドを呼び出します。 `ConfigureServices`

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT ベアラー認証

*Startup.cs*で次の変更を行います。
- メソッドの`UseJwtBearerAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddJwtBearer`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    このコードスニペットはをIdentity使用しないため、既定のスキームは`JwtBearerDefaults.AuthenticationScheme` `AddAuthentication`メソッドに渡すことによって設定する必要があります。

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) 認証

*Startup.cs*で次の変更を行います。

- メソッドの`UseOpenIdConnectAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddOpenIdConnect`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- アクションの`PostLogoutRedirectUri`プロパティを次のよう`SignedOutRedirectUri`に置き換えます。 `OpenIdConnectOptions`

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook での認証

*Startup.cs*で次の変更を行います。
- メソッドの`UseFacebookAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddFacebook`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google での認証

*Startup.cs*で次の変更を行います。
- メソッドの`UseGoogleAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddGoogle`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft アカウント認証

Microsoft アカウント認証の詳細については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/14455)を参照してください。

*Startup.cs*で次の変更を行います。
- メソッドの`UseMicrosoftAccountAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddMicrosoftAccount`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter での認証

*Startup.cs*で次の変更を行います。
- メソッドの`UseTwitterAuthentication`メソッド呼び出しを次のよう`UseAuthentication`に置き換えます。 `Configure`

    ```csharp
    app.UseAuthentication();
    ```

- `ConfigureServices`メソッドで`AddTwitter`メソッドを呼び出します。

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>既定の認証方式を設定する

1.x では、 [Authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基本`AutomaticChallenge`クラスのプロパティ`AutomaticAuthenticate`とプロパティが、1つの認証スキームで設定されることを意図していました。 これを実施するための適切な方法はありませんでした。

2.0 では、これら2つのプロパティは個別`AuthenticationOptions`のインスタンスのプロパティとして削除されています。 これらは、 *Startup.cs*の`ConfigureServices`メソッド`AddAuthentication`内のメソッド呼び出しで構成できます。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

前のコードスニペットでは、既定のスキームは ( `CookieAuthenticationDefaults.AuthenticationScheme` "cookie") に設定されています。

または、オーバーロードされたバージョン`AddAuthentication`のメソッドを使用して、複数のプロパティを設定します。 次のオーバーロードされたメソッドの例では、既定`CookieAuthenticationDefaults.AuthenticationScheme`のスキームはに設定されています。 認証方式は、個々`[Authorize]`の属性または承認ポリシー内で指定することもできます。

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

次のいずれかの条件に該当する場合は、2.0 で既定のスキームを定義します。
- ユーザーが自動的にサインインするようにするには
- スキーマを指定`[Authorize]`せずに属性または承認ポリシーを使用する

この規則の例外は`AddIdentity`メソッドです。 このメソッドは、cookie を追加し、既定の認証およびチャレンジスキームをアプリケーションクッキー `IdentityConstants.ApplicationScheme`に設定します。 また、既定のサインインスキームを外部 cookie `IdentityConstants.ExternalScheme`に設定します。

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext 認証拡張機能の使用

`IAuthenticationManager`インターフェイスは、1. x 認証システムへのメインエントリポイントです。 名前空間の新しい拡張メソッドの`HttpContext`セットに置き換えられました。 `Microsoft.AspNetCore.Authentication`

たとえば、1. x プロジェクトはプロパティを`Authentication`参照します。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2.0 プロジェクトで、 `Microsoft.AspNetCore.Authentication`名前空間をインポートし、 `Authentication`プロパティ参照を削除します。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 認証 (http.sys/IISIntegration)

Windows 認証には、次の2つのバリエーションがあります。

* ホストは、認証されたユーザーのみを許可します。 このバリエーションは、2.0 の変更の影響を受けません。
* ホストは、匿名ユーザーと認証済みユーザーの両方を許可します。 このバリエーションは、2.0 の変更の影響を受けます。 たとえば、アプリでは[IIS](xref:host-and-deploy/iis/index) [または http.sys レイヤー](xref:fundamentals/servers/httpsys)の匿名ユーザーを許可しますが、コントローラーレベルでユーザーを承認する必要があります。 このシナリオでは、 `Startup.ConfigureServices`メソッドで既定のスキームを設定します。

  [AspNetCore 統合](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)では、既定のスキームを次のように`IISDefaults.AuthenticationScheme`設定します。

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)の場合は、既定のスキームを次のように`HttpSysDefaults.AuthenticationScheme`設定します。

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  既定のスキームを設定しなかった場合は、次の例外で承認 (チャレンジ) 要求を処理できなくなります。

  > `System.InvalidOperationException`: AuthenticationScheme が指定されていません。 DefaultChallengeScheme が見つかりませんでした。

詳細については、「<xref:security/authentication/windowsauth>」を参照してください。

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Identity Cookieoptions インスタンス

2.0 の変更の副作用は、cookie オプションのインスタンスの代わりに名前付きオプションを使用するように切り替えることです。 Identity Cookie スキーム名をカスタマイズする機能は削除されます。

たとえば、1.x プロジェクトでは、[コンストラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を使用し`IdentityCookieOptions`て、パラメーターを*AccountController.cs*および*ManageController.cs*に渡します。 外部クッキー認証スキームは、指定されたインスタンスからアクセスされます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

上記のコンストラクターインジェクションは、2.0 プロジェクトでは不要に`_externalCookieScheme`なり、フィールドを削除することができます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1.x プロジェクトでは、次`_externalCookieScheme`のようにフィールドが使用されていました。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2.0 プロジェクトで、上記のコードを次のコードに置き換えます。 定数`IdentityConstants.ExternalScheme`は、直接使用できます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

次の名前空間`SignOutAsync`をインポートして、新しく追加された呼び出しを解決します。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>ユーザーの POCO ナビゲーションプロパティの追加

基本`IdentityUser` POCO (PLAIN Old CLR Object) の ENTITY FRAMEWORK (EF) コアナビゲーションプロパティが削除されました。 1.x プロジェクトでこれらのプロパティを使用していた場合は、手動で2.0 プロジェクトに追加します。

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

EF Core 移行の実行時に外部キーが重複しないようにする`IdentityDbContext`には`OnModelCreating` 、クラスのメソッド`base.OnModelCreating();` (呼び出しの後) に次のを追加します。

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>GetExternalAuthenticationSchemes の置換

非同期バージョンを`GetExternalAuthenticationSchemes`優先するため、同期メソッドが削除されました。 1.x プロジェクトでは、 *Controllers/ManageController*に次のコードが含まれています。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

このメソッドは*Views/Account/Login. cshtml*にも表示されます。

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

2.0 プロジェクトでは、 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>メソッドを使用します。 *ManageController.cs*の変更は、次のコードのようになります。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login. cshtml*で、 `AuthenticationScheme` `foreach`ループでアクセスされるプロパティが次`Name`のように変更されます。

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel プロパティの変更

`ManageLoginsViewModel`オブジェクトは、 `ManageLogins` *ManageController.cs*のアクションで使用されます。 1.x プロジェクトでは、オブジェクトの`OtherLogins`プロパティの戻り値の型`IList<AuthenticationDescription>`はです。 この戻り値の型をインポート`Microsoft.AspNetCore.Http.Authentication`するには、次のものが必要です。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2.0 プロジェクトでは、戻り値の型`IList<AuthenticationScheme>`はに変更されます。 この新しい戻り値の型で`Microsoft.AspNetCore.Http.Authentication`は、インポート`Microsoft.AspNetCore.Authentication`をインポートに置き換える必要があります。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>その他のリソース

詳細については、GitHub で[の Auth 2.0 の問題の説明](https://github.com/aspnet/Security/issues/1338)を参照してください。
