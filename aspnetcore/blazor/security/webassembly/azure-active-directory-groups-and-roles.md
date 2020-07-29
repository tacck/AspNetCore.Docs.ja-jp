---
title: Azure Active Directory のグループとロールを使用する ASP.NET Core [Blazor WebAssembly
author: guardrex
description: Azure Active Directory のグループとロールを使用するように [Blazor WebAssembly を構成する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402157"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="73e9e-103">Azure AD のグループ、管理者ロール、ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="73e9e-104">作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="73e9e-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="73e9e-105">Azure Active Directory (AAD) には、ASP.NET Core [Identity と組み合わせることができる承認方法がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="73e9e-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core [Identity:</span></span>

* <span data-ttu-id="73e9e-106">ユーザー定義グループ</span><span class="sxs-lookup"><span data-stu-id="73e9e-106">User-defined groups</span></span>
  * <span data-ttu-id="73e9e-107">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="73e9e-107">Security</span></span>
  * <span data-ttu-id="73e9e-108">O365</span><span class="sxs-lookup"><span data-stu-id="73e9e-108">O365</span></span>
  * <span data-ttu-id="73e9e-109">配布</span><span class="sxs-lookup"><span data-stu-id="73e9e-109">Distribution</span></span>
* <span data-ttu-id="73e9e-110">役割</span><span class="sxs-lookup"><span data-stu-id="73e9e-110">Roles</span></span>
  * <span data-ttu-id="73e9e-111">組み込み管理者ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="73e9e-112">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-112">User-defined roles</span></span>

<span data-ttu-id="73e9e-113">この記事のガイダンスは、次のトピックで説明されている [Blazor WebAssembly AAD デプロイ シナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-113">The guidance in this article applies to the [Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="73e9e-114">Microsoft アカウントによるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="73e9e-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="73e9e-115">AAD によるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="73e9e-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="73e9e-116">AAD によるホスティング</span><span class="sxs-lookup"><span data-stu-id="73e9e-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="73e9e-117">ユーザー定義グループと組み込み管理者ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="73e9e-118">`groups` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="73e9e-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="73e9e-119">ユーザー定義 AAD グループと組み込み管理者ロールにユーザーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="73e9e-120">Azure AD セキュリティ グループを使用したロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="73e9e-121">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="73e9e-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="73e9e-122">次の例では、ユーザーは AAD の組み込み "*課金管理者*" ロールに割り当てられているものとします。</span><span class="sxs-lookup"><span data-stu-id="73e9e-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="73e9e-123">AAD によって送信される単一の `groups` 要求では、ユーザーのグループとロールが JSON 配列内のオブジェクト ID (GUID) として示されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="73e9e-124">アプリでは、グループとロールの JSON 配列を、アプリで[ポリシー](xref:security/authorization/policies)を作成できる個々の `group` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73e9e-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="73e9e-125"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して、グループとロールの配列プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="73e9e-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="73e9e-126">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="73e9e-127">スタンドアロン アプリまたはホストされているソリューションのクライアント アプリで、カスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="73e9e-128">次のファクトリも、`roles` 要求配列を処理するように構成されます。これについては、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.[Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.[Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="73e9e-129">元の `groups` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73e9e-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="73e9e-130">スタンドアロン アプリまたはホストされているソリューションのクライアント アプリの `Program.Main` (`Program.cs`) に、ファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="73e9e-131">`Program.Main` で、グループごとまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="73e9e-132">次の例では、AAD の組み込み "*課金管理者*" ロールに対するポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="73e9e-133">AAD ロール オブジェクト ID の完全な一覧については、「[AAD の管理者ロールのグループ ID](#aad-adminstrative-role-group-ids)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73e9e-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="73e9e-134">次の例のアプリでは、前述のポリシーを使用してユーザーを承認します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="73e9e-135">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="73e9e-136">コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="73e9e-137">ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="73e9e-138">ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="73e9e-139">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-139">User-defined roles</span></span>

<span data-ttu-id="73e9e-140">ユーザー定義ロールを使用するように、AAD 登録済みアプリを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="73e9e-141">`roles` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="73e9e-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="73e9e-142">以下の例では、次の 2 つのロールがアプリに構成されているものとします。</span><span class="sxs-lookup"><span data-stu-id="73e9e-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="73e9e-143">Azure AD Premium アカウントがないとセキュリティ グループにロールを割り当てることはできませんが、ユーザーをロールに割り当てて、Standard Azure アカウントを使用するユーザーに対する `roles` 要求を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="73e9e-144">このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="73e9e-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="73e9e-145">Azure portal で各追加ロール割り当てに対して**_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="73e9e-146">AAD によって送信される単一の `roles` 要求では、ユーザー定義のロールは JSON 配列内の `appRoles` の `value` として示されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="73e9e-147">アプリでは、ロールの JSON 配列を個々の `role` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73e9e-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="73e9e-148">「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されている `CustomUserFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="73e9e-149">「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されているように、スタンドアロン アプリまたはホストされているソリューションのクライアント アプリで `CustomUserFactory` を追加して登録します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="73e9e-150">元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73e9e-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="73e9e-151">スタンドアロン アプリまたはホストされているソリューションのクライアント アプリの `Program.Main` で、ロール要求として "`role`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="73e9e-152">この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="73e9e-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="73e9e-153">コンポーネント内のすべての承認メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="73e9e-154">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="73e9e-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="73e9e-155">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="73e9e-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="73e9e-156">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="73e9e-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="73e9e-157">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="73e9e-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="73e9e-158">AAD の管理者ロールのグループ ID</span><span class="sxs-lookup"><span data-stu-id="73e9e-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="73e9e-159">次の表で示されているオブジェクト ID は、`group` 要求に対する[ポリシー](xref:security/authorization/policies)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="73e9e-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="73e9e-160">ポリシーでは、アプリ内のさまざまなアクティビティについてユーザーを承認することをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="73e9e-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="73e9e-161">詳細については、「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="73e9e-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="73e9e-162">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="73e9e-162">AAD Administrative Role</span></span> | <span data-ttu-id="73e9e-163">Object ID</span><span class="sxs-lookup"><span data-stu-id="73e9e-163">Object ID</span></span>
--- | ---
<span data-ttu-id="73e9e-164">アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-164">Application administrator</span></span> | <span data-ttu-id="73e9e-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="73e9e-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="73e9e-166">アプリケーション開発者</span><span class="sxs-lookup"><span data-stu-id="73e9e-166">Application developer</span></span> | <span data-ttu-id="73e9e-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="73e9e-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="73e9e-168">認証管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-168">Authentication administrator</span></span> | <span data-ttu-id="73e9e-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="73e9e-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="73e9e-170">Azure DevOps 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-170">Azure DevOps administrator</span></span> | <span data-ttu-id="73e9e-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="73e9e-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="73e9e-172">Azure Information Protection 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="73e9e-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="73e9e-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="73e9e-174">B2C IEF キーセット管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="73e9e-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="73e9e-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="73e9e-176">B2C IEF ポリシー管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="73e9e-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="73e9e-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="73e9e-178">B2C ユーザー フロー管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-178">B2C user flow administrator</span></span> | <span data-ttu-id="73e9e-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="73e9e-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="73e9e-180">B2C ユーザー フロー属性管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="73e9e-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="73e9e-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="73e9e-182">課金管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-182">Billing administrator</span></span> | <span data-ttu-id="73e9e-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="73e9e-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="73e9e-184">クラウド アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-184">Cloud application administrator</span></span> | <span data-ttu-id="73e9e-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="73e9e-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="73e9e-186">クラウド デバイス管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-186">Cloud device administrator</span></span> | <span data-ttu-id="73e9e-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="73e9e-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="73e9e-188">コンプライアンス管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-188">Compliance administrator</span></span> | <span data-ttu-id="73e9e-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="73e9e-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="73e9e-190">コンプライアンス データ管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-190">Compliance data administrator</span></span> | <span data-ttu-id="73e9e-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="73e9e-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="73e9e-192">条件付きアクセス管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-192">Conditional Access administrator</span></span> | <span data-ttu-id="73e9e-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="73e9e-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="73e9e-194">カスタマー ロックボックスのアクセス承認者</span><span class="sxs-lookup"><span data-stu-id="73e9e-194">Customer LockBox access approver</span></span> | <span data-ttu-id="73e9e-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="73e9e-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="73e9e-196">デスクトップ Analytics 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="73e9e-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="73e9e-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="73e9e-198">ディレクトリ閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-198">Directory readers</span></span> | <span data-ttu-id="73e9e-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="73e9e-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="73e9e-200">Dynamics 365 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="73e9e-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="73e9e-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="73e9e-202">Exchange 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-202">Exchange administrator</span></span> | <span data-ttu-id="73e9e-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="73e9e-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="73e9e-204">外部 [IdentityID プロバイダー管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-204">External [Identity Provider administrator</span></span> | <span data-ttu-id="73e9e-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="73e9e-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="73e9e-206">全体管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-206">Global administrator</span></span> | <span data-ttu-id="73e9e-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="73e9e-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="73e9e-208">グローバル閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-208">Global reader</span></span> | <span data-ttu-id="73e9e-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="73e9e-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="73e9e-210">グループ管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-210">Groups administrator</span></span> | <span data-ttu-id="73e9e-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="73e9e-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="73e9e-212">ゲスト招待元</span><span class="sxs-lookup"><span data-stu-id="73e9e-212">Guest inviter</span></span> | <span data-ttu-id="73e9e-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="73e9e-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="73e9e-214">ヘルプデスク管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-214">Helpdesk administrator</span></span> | <span data-ttu-id="73e9e-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="73e9e-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="73e9e-216">Intune 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-216">Intune administrator</span></span> | <span data-ttu-id="73e9e-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="73e9e-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="73e9e-218">Kaizala 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-218">Kaizala administrator</span></span> | <span data-ttu-id="73e9e-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="73e9e-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="73e9e-220">ライセンス管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-220">License administrator</span></span> | <span data-ttu-id="73e9e-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="73e9e-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="73e9e-222">メッセージ センターのプライバシー閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-222">Message center privacy reader</span></span> | <span data-ttu-id="73e9e-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="73e9e-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="73e9e-224">メッセージ センター閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-224">Message center reader</span></span> | <span data-ttu-id="73e9e-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="73e9e-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="73e9e-226">Office アプリ管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-226">Office apps administrator</span></span> | <span data-ttu-id="73e9e-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="73e9e-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="73e9e-228">パスワード管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-228">Password administrator</span></span> | <span data-ttu-id="73e9e-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="73e9e-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="73e9e-230">Power BI 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-230">Power BI administrator</span></span> | <span data-ttu-id="73e9e-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="73e9e-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="73e9e-232">Power Platform 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-232">Power platform administrator</span></span> | <span data-ttu-id="73e9e-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="73e9e-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="73e9e-234">特権認証管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-234">Privileged authentication administrator</span></span> | <span data-ttu-id="73e9e-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="73e9e-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="73e9e-236">特権ロール管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-236">Privileged role administrator</span></span> | <span data-ttu-id="73e9e-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="73e9e-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="73e9e-238">レポート閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-238">Reports reader</span></span> | <span data-ttu-id="73e9e-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="73e9e-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="73e9e-240">Search 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-240">Search administrator</span></span> | <span data-ttu-id="73e9e-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="73e9e-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="73e9e-242">Search エディター</span><span class="sxs-lookup"><span data-stu-id="73e9e-242">Search editor</span></span> | <span data-ttu-id="73e9e-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="73e9e-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="73e9e-244">セキュリティ管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-244">Security administrator</span></span> | <span data-ttu-id="73e9e-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="73e9e-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="73e9e-246">セキュリティ オペレーター</span><span class="sxs-lookup"><span data-stu-id="73e9e-246">Security operator</span></span> | <span data-ttu-id="73e9e-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="73e9e-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="73e9e-248">セキュリティ閲覧者</span><span class="sxs-lookup"><span data-stu-id="73e9e-248">Security reader</span></span> | <span data-ttu-id="73e9e-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="73e9e-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="73e9e-250">サービス サポート管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-250">Service support administrator</span></span> | <span data-ttu-id="73e9e-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="73e9e-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="73e9e-252">SharePoint 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-252">SharePoint administrator</span></span> | <span data-ttu-id="73e9e-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="73e9e-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="73e9e-254">Skype for Business 管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-254">Skype for Business administrator</span></span> | <span data-ttu-id="73e9e-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="73e9e-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="73e9e-256">Teams 通信管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-256">Teams Communications Administrator</span></span> | <span data-ttu-id="73e9e-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="73e9e-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="73e9e-258">Teams 通信サポート エンジニア</span><span class="sxs-lookup"><span data-stu-id="73e9e-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="73e9e-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="73e9e-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="73e9e-260">Teams 通信スペシャリスト</span><span class="sxs-lookup"><span data-stu-id="73e9e-260">Teams Communications Specialist</span></span> | <span data-ttu-id="73e9e-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="73e9e-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="73e9e-262">Teams サービス管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-262">Teams Service Administrator</span></span> | <span data-ttu-id="73e9e-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="73e9e-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="73e9e-264">ユーザー管理者</span><span class="sxs-lookup"><span data-stu-id="73e9e-264">User administrator</span></span> | <span data-ttu-id="73e9e-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="73e9e-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73e9e-266">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="73e9e-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
