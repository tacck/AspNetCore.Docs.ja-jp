---
title: ASP.NET Core での multi-factor authentication
author: damienbod
description: ASP.NET Core アプリで multi-factor authentication (MFA) を設定する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/mfa
ms.openlocfilehash: 873f7d113df84c931ad7fbf2c72aa292e4e87c48
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060392"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="b2d44-103">ASP.NET Core での multi-factor authentication</span><span class="sxs-lookup"><span data-stu-id="b2d44-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="b2d44-104">[Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="b2d44-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="b2d44-105">サンプルコードの表示またはダウンロード (damienbod/AspNetCoreHybridFlowWithApi GitHub リポジトリ)</span><span class="sxs-lookup"><span data-stu-id="b2d44-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="b2d44-106">Multi-factor authentication (MFA) は、追加の形式の識別のためにサインインイベント中にユーザーが要求されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="b2d44-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="b2d44-107">このプロンプトでは、cellphone からコードを入力したり、FIDO2 キーを使用したり、指紋スキャンを提供したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="b2d44-108">2番目の形式の認証が必要な場合は、セキュリティが強化されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="b2d44-109">追加の要因は、攻撃者が簡単に取得したり複製したりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b2d44-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="b2d44-110">この記事では、次の項目について説明します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-110">This article covers the following areas:</span></span>

* <span data-ttu-id="b2d44-111">MFA とは何か、推奨される MFA フロー</span><span class="sxs-lookup"><span data-stu-id="b2d44-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="b2d44-112">を使用して管理ページの MFA を構成する ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b2d44-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="b2d44-113">MFA のサインイン要件を OpenID Connect サーバーに送信する</span><span class="sxs-lookup"><span data-stu-id="b2d44-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="b2d44-114">MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する</span><span class="sxs-lookup"><span data-stu-id="b2d44-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="b2d44-115">MFA、2FA</span><span class="sxs-lookup"><span data-stu-id="b2d44-115">MFA, 2FA</span></span>

<span data-ttu-id="b2d44-116">MFA には、既知の id、所有しているもの、認証するユーザーの生体認証の検証など、id に対して少なくとも2種類の証明が必要です。</span><span class="sxs-lookup"><span data-stu-id="b2d44-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="b2d44-117">2要素認証 (2FA) は MFA のサブセットに似ていますが、MFA の違いは、id を証明するために2つ以上の要因が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="b2d44-118">MFA TOTP (時間ベースのワンタイムパスワードアルゴリズム)</span><span class="sxs-lookup"><span data-stu-id="b2d44-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="b2d44-119">TOTP を使用した MFA は、を使用したサポートされている実装です ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="b2d44-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="b2d44-120">これは、次のようなすべての準拠認証アプリと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="b2d44-121">Microsoft Authenticator アプリ</span><span class="sxs-lookup"><span data-stu-id="b2d44-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="b2d44-122">Google Authenticator アプリ</span><span class="sxs-lookup"><span data-stu-id="b2d44-122">Google Authenticator App</span></span>

<span data-ttu-id="b2d44-123">実装の詳細については、次のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b2d44-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="b2d44-124">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="b2d44-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="b2d44-125">MFA FIDO2 またはパスワードなし</span><span class="sxs-lookup"><span data-stu-id="b2d44-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="b2d44-126">現在、FIDO2 は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b2d44-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="b2d44-127">MFA を実現する最も安全な方法です。</span><span class="sxs-lookup"><span data-stu-id="b2d44-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="b2d44-128">フィッシング攻撃から保護する唯一の MFA フロー。</span><span class="sxs-lookup"><span data-stu-id="b2d44-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="b2d44-129">現時点では、ASP.NET Core は FIDO2 を直接サポートしていません。</span><span class="sxs-lookup"><span data-stu-id="b2d44-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="b2d44-130">FIDO2 は、MFA またはパスワードの少ないフローに使用できます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="b2d44-131">Azure Active Directory では、FIDO2 およびパスワードなしフローがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b2d44-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="b2d44-132">詳細については、「 [Azure Active Directory の Passwordless 認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b2d44-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="b2d44-133">MFA SMS</span><span class="sxs-lookup"><span data-stu-id="b2d44-133">MFA SMS</span></span>

<span data-ttu-id="b2d44-134">SMS による MFA は、パスワード認証 (単一要素) と比較して、セキュリティが大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="b2d44-135">ただし、2番目の要素として SMS を使用することは推奨されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="b2d44-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="b2d44-136">この種類の実装には、既知の攻撃ベクトルが多すぎます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="b2d44-137">NIST のガイドライン</span><span class="sxs-lookup"><span data-stu-id="b2d44-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="b2d44-138">を使用して管理ページの MFA を構成する ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b2d44-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="b2d44-139">MFA は、ユーザーがアプリ内の機密性の高いページにアクセスすることを強制される可能性があり ASP.NET Core Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="b2d44-140">これは、異なる id に対して異なるレベルのアクセスが存在するアプリに便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="b2d44-141">たとえば、ユーザーはパスワードログインを使用してプロファイルデータを表示できますが、管理者は MFA を使用して管理ページにアクセスする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="b2d44-142">MFA 要求を使用してログインを拡張する</span><span class="sxs-lookup"><span data-stu-id="b2d44-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="b2d44-143">デモコードは、およびページと ASP.NET Core を使用して設定し Identity Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="b2d44-144">`AddIdentity`メソッドは1つではなく使用されるので、 `AddDefaultIdentity` `IUserClaimsPrincipalFactory` ログインが成功した後で、実装を使用して id にクレームを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="b2d44-145">クラスは、 `AdditionalUserClaimsPrincipalFactory` `amr` ログインが成功した後にのみ、要求をユーザー要求に追加します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="b2d44-146">要求の値がデータベースから読み取られます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-146">The claim's value is read from the database.</span></span> <span data-ttu-id="b2d44-147">Id が MFA でログインしている場合は、保護されたビューにユーザーがアクセスする必要があるため、この要求はここに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="b2d44-148">データベースビューが要求を使用せずにデータベースから直接読み取られた場合は、MFA をアクティブ化した後に、MFA を使用せずにビューに直接アクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="b2d44-149">クラスでサービスのセットアップが変更されたため、の Identity `Startup` レイアウトを Identity 更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="b2d44-150">ページを Identity アプリにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="b2d44-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="b2d44-151">*Identity /アカウント/manage/_Layout* ファイルでレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="b2d44-152">また、すべての管理ページのレイアウトをページから割り当て Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="b2d44-153">[管理] ページで MFA の要件を検証する</span><span class="sxs-lookup"><span data-stu-id="b2d44-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="b2d44-154">管理ページは、 Razor ユーザーが MFA を使用してログインしたことを検証します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="b2d44-155">メソッドでは、 `OnGet` ユーザー要求にアクセスするために id が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="b2d44-156">`amr`要求の値がチェックされ `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="b2d44-157">Id にこの要求がない場合、またはがの場合 `false` 、ページは [MFA を有効にする] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="b2d44-158">これは、ユーザーが既にログインしていて、MFA がないために発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-158">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="b2d44-159">ユーザーのログイン情報を切り替える UI ロジック</span><span class="sxs-lookup"><span data-stu-id="b2d44-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="b2d44-160">スタートアップ時に承認ポリシーが追加されました。</span><span class="sxs-lookup"><span data-stu-id="b2d44-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="b2d44-161">このポリシーでは、 `amr` という値を持つクレームが必要です `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="b2d44-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="b2d44-162">このポリシーをビューで使用する `_Layout` と、 **管理者** メニューの表示と非表示を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="b2d44-163">Id が MFA を使用してログインしている場合は、[ **管理者** ] メニューが表示され、ツールヒントの警告は表示されません。</span><span class="sxs-lookup"><span data-stu-id="b2d44-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="b2d44-164">ユーザーが MFA なしでログインした場合、ユーザーに通知するツールヒントと共に [ **管理者 (無効)** ] メニューが表示されます (警告について説明しています)。</span><span class="sxs-lookup"><span data-stu-id="b2d44-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="b2d44-165">ユーザーが MFA を使用せずにログインすると、次の警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-165">If the user logs in without MFA, the warning is displayed:</span></span>

![管理者の MFA 認証](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="b2d44-167">ユーザーは、[ **管理者** ] リンクをクリックすると、MFA の有効化ビューにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![管理者が MFA 認証をアクティブ化する](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="b2d44-169">MFA のサインイン要件を OpenID Connect サーバーに送信する</span><span class="sxs-lookup"><span data-stu-id="b2d44-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="b2d44-170">パラメーターを使用すると、 `acr_values` `mfa` 認証要求でクライアントからサーバーに必要な値を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="b2d44-171">`acr_values`これを機能させるには、OpenID connect サーバーでパラメーターを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="b2d44-172">OpenID Connect ASP.NET Core クライアント</span><span class="sxs-lookup"><span data-stu-id="b2d44-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="b2d44-173">ASP.NET Core Razor Pages Openid connect クライアントアプリは、メソッドを使用して `AddOpenIdConnect` openid connect サーバーにログインします。</span><span class="sxs-lookup"><span data-stu-id="b2d44-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="b2d44-174">`acr_values`パラメーターに値を設定し、 `mfa` 認証要求と共に送信します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="b2d44-175">`OpenIdConnectEvents`は、このを追加するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="b2d44-176">推奨される `acr_values` パラメーター値については、「 [認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b2d44-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="b2d44-177">例とし Identity て使用する OpenID connect server 4 サーバー ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b2d44-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="b2d44-178">MVC ビューでを使用して実装された OpenID Connect サーバーで ASP.NET Core Identity は、 *ErrorEnable2FA* という名前の新しいビューが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="b2d44-179">ビュー:</span><span class="sxs-lookup"><span data-stu-id="b2d44-179">The view:</span></span>

* <span data-ttu-id="b2d44-180">が MFA を Identity 必要とするアプリからのものの、ユーザーがでこれをアクティブ化していない場合に表示され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="b2d44-181">ユーザーに通知し、このをアクティブにするためのリンクを追加します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-181">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="b2d44-182">メソッドで `Login` `IIdentityServerInteractionService` `_interaction` は、OpenID connect 要求パラメーターにアクセスするためにインターフェイスの実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="b2d44-183">パラメーターには `acr_values` 、プロパティを使用してアクセスし `AcrValues` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="b2d44-184">クライアントがこれを set と共に送信すると `mfa` 、これを確認できます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="b2d44-185">MFA が必要であり、のユーザーが MFA を有効にしている場合、 ASP.NET Core Identity ログインは続行されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="b2d44-186">ユーザーが MFA を有効にしていない場合、ユーザーはカスタムビュー *ErrorEnable2FA* にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml* .</span></span> <span data-ttu-id="b2d44-187">次 ASP.NET Core Identity に、でユーザーに署名します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-187">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="b2d44-188">メソッドは、 `ExternalLoginCallback` ローカルログインと同様に機能し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="b2d44-189">`AcrValues`プロパティの値がチェックされ `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="b2d44-190">値が存在する場合は、 `mfa` ログインが完了する前に MFA が強制されます (たとえば、ビューにリダイレクトされ `ErrorEnable2FA` ます)。</span><span class="sxs-lookup"><span data-stu-id="b2d44-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="b2d44-191">ユーザーが既にログインしている場合、クライアントアプリは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="b2d44-192">は引き続きクレームを検証 `amr` します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="b2d44-193">ビューへのリンクを使用して MFA を設定でき ASP.NET Core Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="b2d44-195">MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する</span><span class="sxs-lookup"><span data-stu-id="b2d44-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="b2d44-196">この例では、 Razor OpenID connect を使用してサインインする ASP.NET Core ページアプリで、ユーザーが MFA を使用して認証されていることを要求できる方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b2d44-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="b2d44-197">MFA の要件を検証するために、 `IAuthorizationRequirement` 要件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="b2d44-198">これは、MFA を必要とするポリシーを使用してページに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="b2d44-199">`AuthorizationHandler`要求を使用して値をチェックするが実装されてい `amr` `mfa` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="b2d44-200">は `amr` 認証が成功したときに返され、 `id_token` [認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) の指定に定義されているように、さまざまな値を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="b2d44-201">返される値は、id の認証方法と OpenID Connect サーバーの実装によって異なります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="b2d44-202">は `AuthorizationHandler` 要件を使用 `RequireMfa` し、要求を検証し `amr` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="b2d44-203">OpenID Connect サーバーは、と共にサーバー4を使用して実装でき Identity ASP.NET Core Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="b2d44-204">ユーザーが TOTP を使用してログインすると、 `amr` 要求は MFA 値と共に返されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="b2d44-205">異なる OpenID Connect サーバーの実装または別の MFA の種類を使用している場合、 `amr` 要求は異なる値を持つことになります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="b2d44-206">このコードも、このコードを受け入れるように拡張する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-206">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="b2d44-207">メソッドで `Startup.ConfigureServices` `AddOpenIdConnect` は、メソッドが既定のチャレンジスキームとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="b2d44-208">要求を確認するために使用される承認ハンドラー `amr` が、コントロールの反転コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="b2d44-209">その後、要件を追加するポリシーが作成され `RequireMfa` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="b2d44-210">このポリシーは、必要に応じてページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="b2d44-211">ポリシーは、アプリ全体に対してグローバルに追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-211">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="b2d44-212">ユーザーが MFA を使用せずに認証する場合、要求には `amr` 値が設定されている可能性があり `pwd` ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="b2d44-213">要求は、ページへのアクセスを承認されません。</span><span class="sxs-lookup"><span data-stu-id="b2d44-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="b2d44-214">既定値を使用すると、ユーザーは [ *アカウント/AccessDenied* ] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="b2d44-215">この動作は変更できます。または、独自のカスタムロジックを実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="b2d44-216">この例では、有効なユーザーが自分のアカウントに対して MFA を設定できるように、リンクが追加されています。</span><span class="sxs-lookup"><span data-stu-id="b2d44-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="b2d44-217">これで、MFA で認証されるユーザーのみがページまたは web サイトにアクセスできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b2d44-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="b2d44-218">異なる種類の MFA が使用されている場合、または2FA が正常な場合、 `amr` 要求は異なる値を持ち、正しく処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="b2d44-219">また、異なる OpenID Connect サーバーは、この要求に対して異なる値を返します。また、 [認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) の指定に従っていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b2d44-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="b2d44-220">MFA を使用せずにログインする場合 (パスワードのみを使用する場合など):</span><span class="sxs-lookup"><span data-stu-id="b2d44-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="b2d44-221">の `amr` 値は `pwd` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b2d44-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="b2d44-223">アクセスが拒否されました:</span><span class="sxs-lookup"><span data-stu-id="b2d44-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="b2d44-225">または、次の方法で OTP を使用してログインし Identity ます。</span><span class="sxs-lookup"><span data-stu-id="b2d44-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="b2d44-227">その他の資料</span><span class="sxs-lookup"><span data-stu-id="b2d44-227">Additional resources</span></span>

* [<span data-ttu-id="b2d44-228">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="b2d44-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="b2d44-229">Azure Active Directory のパスワードレス認証オプション</span><span class="sxs-lookup"><span data-stu-id="b2d44-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="b2d44-230">.NET を使用した FIDO2/WebAuthn 構成証明およびアサーション用の FIDO2 .NET ライブラリ</span><span class="sxs-lookup"><span data-stu-id="b2d44-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="b2d44-231">WebAuthn すばらしい</span><span class="sxs-lookup"><span data-stu-id="b2d44-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
