---
title: ASP.NET Core での multi-factor authentication
author: damienbod
description: ASP.NET Core アプリで multi-factor authentication (MFA) を設定する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: 4538030b4ce6aba6c78edb69cf44fc5812ddff76
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017858"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="1e6ae-103">ASP.NET Core での multi-factor authentication</span><span class="sxs-lookup"><span data-stu-id="1e6ae-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="1e6ae-104">[Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="1e6ae-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="1e6ae-105">Multi-factor authentication (MFA) は、追加の形式の識別のためにサインインイベント中にユーザーが要求されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="1e6ae-106">このプロンプトでは、cellphone からコードを入力したり、FIDO2 キーを使用したり、指紋スキャンを提供したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="1e6ae-107">2番目の形式の認証が必要な場合は、セキュリティが強化されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="1e6ae-108">追加の要因は、攻撃者が簡単に取得したり複製したりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="1e6ae-109">この記事では、次の項目について説明します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-109">This article covers the following areas:</span></span>

* <span data-ttu-id="1e6ae-110">MFA とは何か、推奨される MFA フロー</span><span class="sxs-lookup"><span data-stu-id="1e6ae-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="1e6ae-111">ASP.NET Core を使用して管理ページの MFA を構成するIdentity</span><span class="sxs-lookup"><span data-stu-id="1e6ae-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="1e6ae-112">MFA のサインイン要件を OpenID Connect サーバーに送信する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="1e6ae-113">MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="1e6ae-114">MFA、2FA</span><span class="sxs-lookup"><span data-stu-id="1e6ae-114">MFA, 2FA</span></span>

<span data-ttu-id="1e6ae-115">MFA には、既知の id、所有しているもの、認証するユーザーの生体認証の検証など、id に対して少なくとも2種類の証明が必要です。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="1e6ae-116">2要素認証 (2FA) は MFA のサブセットに似ていますが、MFA の違いは、id を証明するために2つ以上の要因が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="1e6ae-117">MFA TOTP (時間ベースのワンタイムパスワードアルゴリズム)</span><span class="sxs-lookup"><span data-stu-id="1e6ae-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="1e6ae-118">TOTP を使用した MFA は、ASP.NET Core を使用した実装でサポートされてい Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="1e6ae-119">これは、次のようなすべての準拠認証アプリと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="1e6ae-120">Microsoft Authenticator アプリ</span><span class="sxs-lookup"><span data-stu-id="1e6ae-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="1e6ae-121">Google Authenticator アプリ</span><span class="sxs-lookup"><span data-stu-id="1e6ae-121">Google Authenticator App</span></span>

<span data-ttu-id="1e6ae-122">実装の詳細については、次のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="1e6ae-123">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="1e6ae-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="1e6ae-124">MFA FIDO2 またはパスワードなし</span><span class="sxs-lookup"><span data-stu-id="1e6ae-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="1e6ae-125">現在、FIDO2 は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="1e6ae-126">MFA を実現する最も安全な方法です。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="1e6ae-127">フィッシング攻撃から保護する唯一の MFA フロー。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="1e6ae-128">現時点では、ASP.NET Core は FIDO2 を直接サポートしていません。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="1e6ae-129">FIDO2 は、MFA またはパスワードの少ないフローに使用できます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="1e6ae-130">Azure Active Directory では、FIDO2 およびパスワードなしフローがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="1e6ae-131">詳細については、「 [Azure Active Directory の Passwordless 認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="1e6ae-132">MFA SMS</span><span class="sxs-lookup"><span data-stu-id="1e6ae-132">MFA SMS</span></span>

<span data-ttu-id="1e6ae-133">SMS による MFA は、パスワード認証 (単一要素) と比較して、セキュリティが大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="1e6ae-134">ただし、2番目の要素として SMS を使用することは推奨されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="1e6ae-135">この種類の実装には、既知の攻撃ベクトルが多すぎます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="1e6ae-136">NIST のガイドライン</span><span class="sxs-lookup"><span data-stu-id="1e6ae-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-no-locidentity"></a><span data-ttu-id="1e6ae-137">ASP.NET Core を使用して管理ページの MFA を構成するIdentity</span><span class="sxs-lookup"><span data-stu-id="1e6ae-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="1e6ae-138">MFA は、ユーザーが ASP.NET Core アプリ内の機密性の高いページにアクセスすることを強制する可能性があり Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="1e6ae-139">これは、異なる id に対して異なるレベルのアクセスが存在するアプリに便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="1e6ae-140">たとえば、ユーザーはパスワードログインを使用してプロファイルデータを表示できますが、管理者は MFA を使用して管理ページにアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="1e6ae-141">MFA 要求を使用してログインを拡張する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="1e6ae-142">デモコードは、およびページと ASP.NET Core を使用して設定し Identity Razor ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="1e6ae-143">`AddIdentity`メソッドは1つではなく使用されるので、 `AddDefaultIdentity` `IUserClaimsPrincipalFactory` ログインが成功した後で、実装を使用して id にクレームを追加できます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="1e6ae-144">クラスは、 `AdditionalUserClaimsPrincipalFactory` `amr` ログインが成功した後にのみ、要求をユーザー要求に追加します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="1e6ae-145">要求の値がデータベースから読み取られます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-145">The claim's value is read from the database.</span></span> <span data-ttu-id="1e6ae-146">Id が MFA でログインしている場合は、保護されたビューにユーザーがアクセスする必要があるため、この要求はここに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="1e6ae-147">データベースビューが要求を使用せずにデータベースから直接読み取られた場合は、MFA をアクティブ化した後に、MFA を使用せずにビューに直接アクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="1e6ae-148">クラスでサービスのセットアップが変更されたため、の Identity `Startup` レイアウトを Identity 更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="1e6ae-149">ページを Identity アプリにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="1e6ae-150">\* Identity /アカウント/manage/_Layout\*ファイルでレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="1e6ae-151">また、すべての管理ページのレイアウトをページから割り当て Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="1e6ae-152">[管理] ページで MFA の要件を検証する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="1e6ae-153">管理ページは、 Razor ユーザーが MFA を使用してログインしたことを検証します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="1e6ae-154">メソッドでは、 `OnGet` ユーザー要求にアクセスするために id が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="1e6ae-155">`amr`要求の値がチェックされ `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="1e6ae-156">Id にこの要求がない場合、またはがの場合 `false` 、ページは [MFA を有効にする] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="1e6ae-157">これは、ユーザーが既にログインしていて、MFA がないために発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-157">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="1e6ae-158">ユーザーのログイン情報を切り替える UI ロジック</span><span class="sxs-lookup"><span data-stu-id="1e6ae-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="1e6ae-159">スタートアップ時に承認ポリシーが追加されました。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="1e6ae-160">このポリシーでは、 `amr` という値を持つクレームが必要です `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="1e6ae-161">このポリシーをビューで使用する `_Layout` と、**管理者**メニューの表示と非表示を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="1e6ae-162">Id が MFA を使用してログインしている場合は、[**管理者**] メニューが表示され、ツールヒントの警告は表示されません。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="1e6ae-163">ユーザーが MFA なしでログインした場合、ユーザーに通知するツールヒントと共に [**管理者 (無効)** ] メニューが表示されます (警告について説明しています)。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="1e6ae-164">ユーザーが MFA を使用せずにログインすると、次の警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-164">If the user logs in without MFA, the warning is displayed:</span></span>

![管理者の MFA 認証](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="1e6ae-166">ユーザーは、[**管理者**] リンクをクリックすると、MFA の有効化ビューにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![管理者が MFA 認証をアクティブ化する](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="1e6ae-168">MFA のサインイン要件を OpenID Connect サーバーに送信する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="1e6ae-169">パラメーターを使用すると、 `acr_values` `mfa` 認証要求でクライアントからサーバーに必要な値を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="1e6ae-170">`acr_values`これを機能させるには、OpenID connect サーバーでパラメーターを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-170">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="1e6ae-171">OpenID Connect ASP.NET Core クライアント</span><span class="sxs-lookup"><span data-stu-id="1e6ae-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="1e6ae-172">ASP.NET Core Razor Pages Openid connect クライアントアプリは、メソッドを使用して `AddOpenIdConnect` openid connect サーバーにログインします。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-172">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="1e6ae-173">`acr_values`パラメーターに値を設定し、 `mfa` 認証要求と共に送信します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="1e6ae-174">`OpenIdConnectEvents`は、このを追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="1e6ae-175">推奨される `acr_values` パラメーター値については、「[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-aspnet-core-no-locidentity"></a><span data-ttu-id="1e6ae-176">IdentityASP.NET Core を使用した OpenID connect server 4 サーバーの例Identity</span><span class="sxs-lookup"><span data-stu-id="1e6ae-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="1e6ae-177">MVC ビューで ASP.NET Core を使用して実装された OpenID Connect サーバーで Identity は、 *ErrorEnable2FA*という名前の新しいビューが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="1e6ae-178">ビュー:</span><span class="sxs-lookup"><span data-stu-id="1e6ae-178">The view:</span></span>

* <span data-ttu-id="1e6ae-179">が MFA を Identity 必要とするアプリからのものの、ユーザーがでこれをアクティブ化していない場合に表示され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="1e6ae-180">ユーザーに通知し、このをアクティブにするためのリンクを追加します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-180">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="1e6ae-181">メソッドで `Login` `IIdentityServerInteractionService` `_interaction` は、OpenID connect 要求パラメーターにアクセスするためにインターフェイスの実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="1e6ae-182">パラメーターには `acr_values` 、プロパティを使用してアクセスし `AcrValues` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="1e6ae-183">クライアントがこれを set と共に送信すると `mfa` 、これを確認できます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="1e6ae-184">MFA が必要で、ASP.NET Core のユーザーが MFA を有効にしている場合、 Identity ログインは続行されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="1e6ae-185">ユーザーが MFA を有効にしていない場合、ユーザーはカスタムビュー *ErrorEnable2FA*にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="1e6ae-186">次 Identity に、ASP.NET Core ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-186">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="1e6ae-187">メソッドは、 `ExternalLoginCallback` ローカルログインと同様に機能し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="1e6ae-188">`AcrValues`プロパティの値がチェックされ `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="1e6ae-189">値が存在する場合は、 `mfa` ログインが完了する前に MFA が強制されます (たとえば、ビューにリダイレクトされ `ErrorEnable2FA` ます)。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="1e6ae-190">ユーザーが既にログインしている場合、クライアントアプリは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="1e6ae-191">は引き続きクレームを検証 `amr` します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="1e6ae-192">ASP.NET Core ビューへのリンクを使用して MFA を設定でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="1e6ae-194">MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する</span><span class="sxs-lookup"><span data-stu-id="1e6ae-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="1e6ae-195">この例では、 Razor OpenID connect を使用してサインインする ASP.NET Core ページアプリで、ユーザーが MFA を使用して認証されていることを要求できる方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="1e6ae-196">MFA の要件を検証するために、 `IAuthorizationRequirement` 要件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="1e6ae-197">これは、MFA を必要とするポリシーを使用してページに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="1e6ae-198">`AuthorizationHandler`要求を使用して値をチェックするが実装されてい `amr` `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="1e6ae-199">は `amr` 認証が成功したときに返され、 `id_token` [認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に定義されているように、さまざまな値を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="1e6ae-200">返される値は、id の認証方法と OpenID Connect サーバーの実装によって異なります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-200">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="1e6ae-201">は `AuthorizationHandler` 要件を使用 `RequireMfa` し、要求を検証し `amr` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="1e6ae-202">OpenID Connect サーバーは、 Identity ASP.NET Core と共にサーバー4を使用して実装でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="1e6ae-203">ユーザーが TOTP を使用してログインすると、 `amr` 要求は MFA 値と共に返されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="1e6ae-204">異なる OpenID Connect サーバーの実装または別の MFA の種類を使用している場合、 `amr` 要求は異なる値を持つことになります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="1e6ae-205">このコードも、このコードを受け入れるように拡張する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-205">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="1e6ae-206">メソッドで `Startup.ConfigureServices` `AddOpenIdConnect` は、メソッドが既定のチャレンジスキームとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="1e6ae-207">要求を確認するために使用される承認ハンドラー `amr` が、コントロールの反転コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="1e6ae-208">その後、要件を追加するポリシーが作成され `RequireMfa` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="1e6ae-209">このポリシーは、必要に応じてページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="1e6ae-210">ポリシーは、アプリ全体に対してグローバルに追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-210">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="1e6ae-211">ユーザーが MFA を使用せずに認証する場合、要求には `amr` 値が設定されている可能性があり `pwd` ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="1e6ae-212">要求は、ページへのアクセスを承認されません。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="1e6ae-213">既定値を使用すると、ユーザーは [*アカウント/AccessDenied* ] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="1e6ae-214">この動作は変更できます。または、独自のカスタムロジックを実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="1e6ae-215">この例では、有効なユーザーが自分のアカウントに対して MFA を設定できるように、リンクが追加されています。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="1e6ae-216">これで、MFA で認証されるユーザーのみがページまたは web サイトにアクセスできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="1e6ae-217">異なる種類の MFA が使用されている場合、または2FA が正常な場合、 `amr` 要求は異なる値を持ち、正しく処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="1e6ae-218">また、異なる OpenID Connect サーバーは、この要求に対して異なる値を返します。また、[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に従っていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-218">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="1e6ae-219">MFA を使用せずにログインする場合 (パスワードのみを使用する場合など):</span><span class="sxs-lookup"><span data-stu-id="1e6ae-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="1e6ae-220">の `amr` 値は `pwd` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="1e6ae-222">アクセスが拒否されました:</span><span class="sxs-lookup"><span data-stu-id="1e6ae-222">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="1e6ae-224">または、次の方法で OTP を使用してログインし Identity ます。</span><span class="sxs-lookup"><span data-stu-id="1e6ae-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="1e6ae-226">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1e6ae-226">Additional resources</span></span>

* [<span data-ttu-id="1e6ae-227">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="1e6ae-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="1e6ae-228">Azure Active Directory のパスワードレス認証オプション</span><span class="sxs-lookup"><span data-stu-id="1e6ae-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="1e6ae-229">.NET を使用した FIDO2/WebAuthn 構成証明およびアサーション用の FIDO2 .NET ライブラリ</span><span class="sxs-lookup"><span data-stu-id="1e6ae-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="1e6ae-230">WebAuthn すばらしい</span><span class="sxs-lookup"><span data-stu-id="1e6ae-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
