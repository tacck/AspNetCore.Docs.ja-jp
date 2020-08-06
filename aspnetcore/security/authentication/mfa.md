---
title: ASP.NET Core での multi-factor authentication
author: damienbod
description: ASP.NET Core アプリで multi-factor authentication (MFA) を設定する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: 1ab6e5802e177aeaf77584838feea09a7ff79db7
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819179"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>ASP.NET Core での multi-factor authentication

[Damien Bowden](https://github.com/damienbod)

Multi-factor authentication (MFA) は、追加の形式の識別のためにサインインイベント中にユーザーが要求されるプロセスです。 このプロンプトでは、cellphone からコードを入力したり、FIDO2 キーを使用したり、指紋スキャンを提供したりすることができます。 2番目の形式の認証が必要な場合は、セキュリティが強化されます。 追加の要因は、攻撃者が簡単に取得したり複製したりすることはできません。

この記事では、次の項目について説明します。

* MFA とは何か、推奨される MFA フロー
* ASP.NET Core を使用して管理ページの MFA を構成するIdentity
* MFA のサインイン要件を OpenID Connect サーバーに送信する
* MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する

## <a name="mfa-2fa"></a>MFA、2FA

MFA には、既知の id、所有しているもの、認証するユーザーの生体認証の検証など、id に対して少なくとも2種類の証明が必要です。

2要素認証 (2FA) は MFA のサブセットに似ていますが、MFA の違いは、id を証明するために2つ以上の要因が必要になる場合があります。

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (時間ベースのワンタイムパスワードアルゴリズム)

TOTP を使用した MFA は、ASP.NET Core を使用した実装でサポートされてい Identity ます。 これは、次のようなすべての準拠認証アプリと共に使用できます。

* Microsoft Authenticator アプリ
* Google Authenticator アプリ

実装の詳細については、次のリンクを参照してください。

[ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 またはパスワードなし

現在、FIDO2 は次のとおりです。

* MFA を実現する最も安全な方法です。
* フィッシング攻撃から保護する唯一の MFA フロー。

現時点では、ASP.NET Core は FIDO2 を直接サポートしていません。 FIDO2 は、MFA またはパスワードの少ないフローに使用できます。

Azure Active Directory では、FIDO2 およびパスワードなしフローがサポートされています。 詳細については、「 [Azure Active Directory の Passwordless 認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)」を参照してください。

### <a name="mfa-sms"></a>MFA SMS

SMS による MFA は、パスワード認証 (単一要素) と比較して、セキュリティが大幅に向上します。 ただし、2番目の要素として SMS を使用することは推奨されなくなりました。 この種類の実装には、既知の攻撃ベクトルが多すぎます。

[NIST のガイドライン](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-no-locidentity"></a>ASP.NET Core を使用して管理ページの MFA を構成するIdentity

MFA は、ユーザーが ASP.NET Core アプリ内の機密性の高いページにアクセスすることを強制する可能性があり Identity ます。 これは、異なる id に対して異なるレベルのアクセスが存在するアプリに便利な場合があります。 たとえば、ユーザーはパスワードログインを使用してプロファイルデータを表示できますが、管理者は MFA を使用して管理ページにアクセスする必要があります。

### <a name="extend-the-login-with-an-mfa-claim"></a>MFA 要求を使用してログインを拡張する

デモコードは、およびページと ASP.NET Core を使用して設定し Identity Razor ます。 `AddIdentity`メソッドは1つではなく使用されるので、 `AddDefaultIdentity` `IUserClaimsPrincipalFactory` ログインが成功した後で、実装を使用して id にクレームを追加できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

クラスは、 `AdditionalUserClaimsPrincipalFactory` `amr` ログインが成功した後にのみ、要求をユーザー要求に追加します。 要求の値がデータベースから読み取られます。 Id が MFA でログインしている場合は、保護されたビューにユーザーがアクセスする必要があるため、この要求はここに追加されます。 データベースビューが要求を使用せずにデータベースから直接読み取られた場合は、MFA をアクティブ化した後に、MFA を使用せずにビューに直接アクセスすることができます。

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

クラスでサービスのセットアップが変更されたため、の Identity `Startup` レイアウトを Identity 更新する必要があります。 ページを Identity アプリにスキャフォールディングします。 * Identity /アカウント/manage/_Layout*ファイルでレイアウトを定義します。

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

また、すべての管理ページのレイアウトをページから割り当て Identity ます。

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>[管理] ページで MFA の要件を検証する

管理ページは、 Razor ユーザーが MFA を使用してログインしたことを検証します。 メソッドでは、 `OnGet` ユーザー要求にアクセスするために id が使用されます。 `amr`要求の値がチェックされ `mfa` ます。 Id にこの要求がない場合、またはがの場合 `false` 、ページは [MFA を有効にする] ページにリダイレクトされます。 これは、ユーザーが既にログインしていて、MFA がないために発生する可能性があります。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>ユーザーのログイン情報を切り替える UI ロジック

スタートアップ時に承認ポリシーが追加されました。 このポリシーでは、 `amr` という値を持つクレームが必要です `mfa` 。

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

このポリシーをビューで使用する `_Layout` と、**管理者**メニューの表示と非表示を切り替えることができます。

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Id が MFA を使用してログインしている場合は、[**管理者**] メニューが表示され、ツールヒントの警告は表示されません。 ユーザーが MFA なしでログインした場合、ユーザーに通知するツールヒントと共に [**管理者 (無効)** ] メニューが表示されます (警告について説明しています)。

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

ユーザーが MFA を使用せずにログインすると、次の警告が表示されます。

![管理者の MFA 認証](mfa/_static/identitystandalonemfa_01.png)

ユーザーは、[**管理者**] リンクをクリックすると、MFA の有効化ビューにリダイレクトされます。

![管理者が MFA 認証をアクティブ化する](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>MFA のサインイン要件を OpenID Connect サーバーに送信する 

パラメーターを使用すると、 `acr_values` `mfa` 認証要求でクライアントからサーバーに必要な値を渡すことができます。

> [!NOTE]
> `acr_values`これを機能させるには、OpenID connect サーバーでパラメーターを処理する必要があります。

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core クライアント

ASP.NET Core Razor Pages Openid connect クライアントアプリは、メソッドを使用して `AddOpenIdConnect` openid connect サーバーにログインします。 `acr_values`パラメーターに値を設定し、 `mfa` 認証要求と共に送信します。 `OpenIdConnectEvents`は、このを追加するために使用されます。

推奨される `acr_values` パラメーター値については、「[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)」を参照してください。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-aspnet-core-no-locidentity"></a>IdentityASP.NET Core を使用した OpenID connect server 4 サーバーの例Identity

MVC ビューで ASP.NET Core を使用して実装された OpenID Connect サーバーで Identity は、 *ErrorEnable2FA*という名前の新しいビューが作成されます。 ビュー:

* が MFA を Identity 必要とするアプリからのものの、ユーザーがでこれをアクティブ化していない場合に表示され Identity ます。
* ユーザーに通知し、このをアクティブにするためのリンクを追加します。

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

メソッドで `Login` `IIdentityServerInteractionService` `_interaction` は、OpenID connect 要求パラメーターにアクセスするためにインターフェイスの実装が使用されます。 パラメーターには `acr_values` 、プロパティを使用してアクセスし `AcrValues` ます。 クライアントがこれを set と共に送信すると `mfa` 、これを確認できます。

MFA が必要で、ASP.NET Core のユーザーが MFA を有効にしている場合、 Identity ログインは続行されます。 ユーザーが MFA を有効にしていない場合、ユーザーはカスタムビュー *ErrorEnable2FA*にリダイレクトされます。 次 Identity に、ASP.NET Core ユーザーにサインインします。

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

メソッドは、 `ExternalLoginCallback` ローカルログインと同様に機能し Identity ます。 `AcrValues`プロパティの値がチェックされ `mfa` ます。 値が存在する場合は、 `mfa` ログインが完了する前に MFA が強制されます (たとえば、ビューにリダイレクトされ `ErrorEnable2FA` ます)。

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

ユーザーが既にログインしている場合、クライアントアプリは次のようになります。

* は引き続きクレームを検証 `amr` します。
* ASP.NET Core ビューへのリンクを使用して MFA を設定でき Identity ます。

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する

この例では、 Razor OpenID connect を使用してサインインする ASP.NET Core ページアプリで、ユーザーが MFA を使用して認証されていることを要求できる方法を示します。

MFA の要件を検証するために、 `IAuthorizationRequirement` 要件が作成されます。 これは、MFA を必要とするポリシーを使用してページに追加されます。

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`AuthorizationHandler`要求を使用して値をチェックするが実装されてい `amr` `mfa` ます。 は `amr` 認証が成功したときに返され、 `id_token` [認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に定義されているように、さまざまな値を持つことができます。

返される値は、id の認証方法と OpenID Connect サーバーの実装によって異なります。

は `AuthorizationHandler` 要件を使用 `RequireMfa` し、要求を検証し `amr` ます。 OpenID Connect サーバーは、 Identity ASP.NET Core と共にサーバー4を使用して実装でき Identity ます。 ユーザーが TOTP を使用してログインすると、 `amr` 要求は MFA 値と共に返されます。 異なる OpenID Connect サーバーの実装または別の MFA の種類を使用している場合、 `amr` 要求は異なる値を持つことになります。 このコードも、このコードを受け入れるように拡張する必要があります。

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

メソッドで `Startup.ConfigureServices` `AddOpenIdConnect` は、メソッドが既定のチャレンジスキームとして使用されます。 要求を確認するために使用される承認ハンドラー `amr` が、コントロールの反転コンテナーに追加されます。 その後、要件を追加するポリシーが作成され `RequireMfa` ます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

このポリシーは、必要に応じてページで使用され Razor ます。 ポリシーは、アプリ全体に対してグローバルに追加することもできます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

ユーザーが MFA を使用せずに認証する場合、要求には `amr` 値が設定されている可能性があり `pwd` ます。 要求は、ページへのアクセスを承認されません。 既定値を使用すると、ユーザーは [*アカウント/AccessDenied* ] ページにリダイレクトされます。 この動作は変更できます。または、独自のカスタムロジックを実装することもできます。 この例では、有効なユーザーが自分のアカウントに対して MFA を設定できるように、リンクが追加されています。

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

これで、MFA で認証されるユーザーのみがページまたは web サイトにアクセスできるようになりました。 異なる種類の MFA が使用されている場合、または2FA が正常な場合、 `amr` 要求は異なる値を持ち、正しく処理する必要があります。 また、異なる OpenID Connect サーバーは、この要求に対して異なる値を返します。また、[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に従っていない可能性があります。

MFA を使用せずにログインする場合 (パスワードのみを使用する場合など):

* の `amr` 値は `pwd` 次のとおりです。

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* アクセスが拒否されました:

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

または、次の方法で OTP を使用してログインし Identity ます。

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする](xref:security/authentication/identity-enable-qrcodes)
* [Azure Active Directory のパスワードレス認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)
* [.NET を使用した FIDO2/WebAuthn 構成証明およびアサーション用の FIDO2 .NET ライブラリ](https://github.com/abergs/fido2-net-lib)
* [WebAuthn すばらしい](https://github.com/herrjemand/awesome-webauthn)
