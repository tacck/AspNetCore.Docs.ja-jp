---
title: ASP.NET Core Blazor の認証と承認
author: guardrex
description: Blazor の認証と承認のシナリオについて説明します。
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
uid: blazor/security/index
ms.openlocfilehash: e905f08f867b73fc37d5fed7138256ac89811312
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402404"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="feb64-103">ASP.NET Core Blazor の認証と承認</span><span class="sxs-lookup"><span data-stu-id="feb64-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="feb64-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="feb64-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="feb64-105">ASP.NET Core は、Blazor アプリのセキュリティの構成と管理をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="feb64-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="feb64-106">Blazor Server アプリと Blazor WebAssembly アプリでは、セキュリティに関するシナリオが異なります。</span><span class="sxs-lookup"><span data-stu-id="feb64-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="feb64-107">Blazor Server アプリはサーバー上で実行するため、承認チェックでは以下のことを決定できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="feb64-108">ユーザーに表示される UI オプション (たとえば、ユーザーが利用できるメニュー エントリ)。</span><span class="sxs-lookup"><span data-stu-id="feb64-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="feb64-109">アプリとコンポーネントの領域に対するアクセス規則。</span><span class="sxs-lookup"><span data-stu-id="feb64-109">Access rules for areas of the app and components.</span></span>

Blazor WebAssembly<span data-ttu-id="feb64-110"> アプリはクライアント上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-110"> apps run on the client.</span></span> <span data-ttu-id="feb64-111">承認は、表示する UI オプションを決定するために "*のみ*" 使用されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="feb64-112">クライアント側のチェックはユーザーによって変更またはバイパスされる可能性があるため、Blazor WebAssembly アプリでは承認アクセス規則を適用できません。</span><span class="sxs-lookup"><span data-stu-id="feb64-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="feb64-113">[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization)は、ルーティング可能な Razor コンポーネントには適用されません。</span><span class="sxs-lookup"><span data-stu-id="feb64-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="feb64-114">ルーティング不可能な Razor コンポーネントが[ページに埋め込まれている](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view)場合、ページの承認規則は、Razor コンポーネントと、ページのコンテンツの残りの部分に間接的に影響します。</span><span class="sxs-lookup"><span data-stu-id="feb64-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="feb64-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> と <xref:Microsoft.AspNetCore.Identity.UserManager%601> は、Razor コンポーネントではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="feb64-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="feb64-116">認証</span><span class="sxs-lookup"><span data-stu-id="feb64-116">Authentication</span></span>

Blazor<span data-ttu-id="feb64-117"> は、既存の ASP.NET Core 認証メカニズムを使用してユーザーの ID を証明します。</span><span class="sxs-lookup"><span data-stu-id="feb64-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="feb64-118">詳細なメカニズムは、Blazor アプリのホスティング方法、Blazor WebAssembly サーバーか Blazor Server かによって異なります。</span><span class="sxs-lookup"><span data-stu-id="feb64-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly<span data-ttu-id="feb64-119"> 認証</span><span class="sxs-lookup"><span data-stu-id="feb64-119"> authentication</span></span>

<span data-ttu-id="feb64-120">Blazor WebAssembly アプリでは、ユーザーがすべてのクライアント側コードを変更できるため、認証チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="feb64-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="feb64-121">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="feb64-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="feb64-122">以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="feb64-122">Add the following:</span></span>

* <span data-ttu-id="feb64-123">アプリのプロジェクト ファイルに、[`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) のパッケージ参照。</span><span class="sxs-lookup"><span data-stu-id="feb64-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="feb64-124">アプリの `_Imports.razor` ファイルに、`Microsoft.AspNetCore.Components.Authorization` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="feb64-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="feb64-125">認証を処理するために、組み込みまたはカスタムの <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> サービスの実装について、以降のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="feb64-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="feb64-126">アプリの作成と構成の詳細については、「<xref:blazor/security/webassembly/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor Server<span data-ttu-id="feb64-127"> 認証</span><span class="sxs-lookup"><span data-stu-id="feb64-127"> authentication</span></span>

Blazor Server<span data-ttu-id="feb64-128"> アプリは、SignalR を使用して作成されたリアルタイム接続を介して動作します。</span><span class="sxs-lookup"><span data-stu-id="feb64-128"> apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="feb64-129">[SignalR ベースのアプリの認証](xref:signalr/authn-and-authz)は、接続が確立したときに処理されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="feb64-130">認証は、Cookie または他のベアラー トークンに基づいています。</span><span class="sxs-lookup"><span data-stu-id="feb64-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="feb64-131">アプリの作成と構成の詳細については、「<xref:blazor/security/server/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="feb64-132">AuthenticationStateProvider サービス</span><span class="sxs-lookup"><span data-stu-id="feb64-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="feb64-133">組み込みの <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> サーバー アプリでは、ASP.NET Core の `HttpContext.User` から認証状態データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="feb64-134">このようにして、認証状態は既存の ASP.NET Core 認証メカニズムと統合されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="feb64-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> は、認証状態を取得するために <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> コンポーネントと <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> コンポーネントによって使用される基となるサービスです。</span><span class="sxs-lookup"><span data-stu-id="feb64-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="feb64-136">通常、<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> は直接使用しません。</span><span class="sxs-lookup"><span data-stu-id="feb64-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="feb64-137">この記事で後述する [`AuthorizeView` コンポーネント](#authorizeview-component)または [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="feb64-138"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> を直接使用することの主な欠点は、基となる認証状態データが変更されてもコンポーネントに自動的に通知されないことです。</span><span class="sxs-lookup"><span data-stu-id="feb64-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="feb64-139">次の例に示すように、<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> サービスから現在のユーザーの <xref:System.Security.Claims.ClaimsPrincipal> データを提供できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="feb64-140">`user.Identity.IsAuthenticated` が `true` である場合、ユーザーが <xref:System.Security.Claims.ClaimsPrincipal> であるため、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="feb64-141">依存関係の注入 (DI) とサービスの詳細については、<xref:blazor/fundamentals/dependency-injection> と <xref:fundamentals/dependency-injection> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="feb64-142">カスタム AuthenticationStateProvider の実装</span><span class="sxs-lookup"><span data-stu-id="feb64-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="feb64-143">アプリでカスタム プロバイダーを必要とする場合は、<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> を実装し、`GetAuthenticationStateAsync` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="feb64-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="feb64-144">Blazor WebAssembly アプリでは、`CustomAuthStateProvider` サービスは `Program.cs` の `Main` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="feb64-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="feb64-145">Blazor Server アプリでは、`CustomAuthStateProvider` サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="feb64-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="feb64-146">前の例で `CustomAuthStateProvider` を使用すると、すべてのユーザーはユーザー名 `mrfibuli` で認証されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="feb64-147">認証状態をカスケード パラメーターとして公開する</span><span class="sxs-lookup"><span data-stu-id="feb64-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="feb64-148">ユーザーによってトリガーされたアクションを実行する場合など、認証状態データが手続き型ロジックに必要な場合は、型 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` のカスケード パラメーターを定義して認証状態データを取得します。</span><span class="sxs-lookup"><span data-stu-id="feb64-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="feb64-149">`user.Identity.IsAuthenticated` が `true` である場合、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="feb64-150">`App` コンポーネント (`App.razor`) 内の <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> および <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> コンポーネントを使用して、`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` カスケード パラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="feb64-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="feb64-151">Blazor WebAssembly アプリでは、オプションと承認のためのサービスを `Program.Main` に追加します。</span><span class="sxs-lookup"><span data-stu-id="feb64-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="feb64-152">Blazor Server アプリでは、オプションと承認のためのサービスが既に存在するため、これ以上の操作は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="feb64-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="feb64-153">承認</span><span class="sxs-lookup"><span data-stu-id="feb64-153">Authorization</span></span>

<span data-ttu-id="feb64-154">ユーザーが認証されると、ユーザーが実行できる操作を制御する "*承認*" 規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="feb64-155">通常、アクセスは以下の条件に基づいて許可または拒否されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="feb64-156">ユーザーが認証されている (サインインしている)。</span><span class="sxs-lookup"><span data-stu-id="feb64-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="feb64-157">ユーザーに "*ロール*" が割り当てられている。</span><span class="sxs-lookup"><span data-stu-id="feb64-157">A user is in a *role*.</span></span>
* <span data-ttu-id="feb64-158">ユーザーに "*要求*" がある。</span><span class="sxs-lookup"><span data-stu-id="feb64-158">A user has a *claim*.</span></span>
* <span data-ttu-id="feb64-159">"*ポリシー*" が満たされている。</span><span class="sxs-lookup"><span data-stu-id="feb64-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="feb64-160">これらの各概念は、ASP.NET Core MVC または Razor Pages アプリと同じです。</span><span class="sxs-lookup"><span data-stu-id="feb64-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="feb64-161">ASP.NET Core のセキュリティの詳細については、[ASP.NET Core のセキュリティと Identity](xref:security/index) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="feb64-162">AuthorizeView コンポーネント</span><span class="sxs-lookup"><span data-stu-id="feb64-162">AuthorizeView component</span></span>

<span data-ttu-id="feb64-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> コンポーネントでは、ユーザーに表示を許可するかどうかに応じて UI が選択的に表示されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="feb64-164">このアプローチは、ユーザーに対してデータを "*表示する*" だけで済み、手続き型ロジックでユーザーの ID を使用する必要がない場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="feb64-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="feb64-165">このコンポーネントでは、型 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> の `context` 変数が公開されており、これを使用して、サインインしたユーザーに関する情報にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="feb64-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="feb64-166">ユーザーが認証されていない場合は、表示用に異なるコンテンツを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="feb64-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="feb64-167"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> コンポーネントは、`NavMenu` コンポーネント (`Shared/NavMenu.razor`) で使用して [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) のリスト項目 (`<li>...</li>`) を表示できますが、この方法では、表示された出力からリスト項目が削除されるだけであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="feb64-168">ユーザーがコンポーネントに移動するのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="feb64-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="feb64-169">`<Authorized>` および `<NotAuthorized>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="feb64-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="feb64-170">UI オプションまたはアクセスを制御するロールやポリシーなどの承認条件については、「[承認](#authorization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="feb64-171">承認条件が指定されていない場合、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> では既定のポリシーが使用され、以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="feb64-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="feb64-172">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="feb64-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="feb64-173">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="feb64-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="feb64-174">ロールベースとリソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="feb64-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="feb64-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> コンポーネントは、"*ロールベース*" または "*ポリシーベース*" の承認をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="feb64-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="feb64-176">ロールベースの承認の場合は、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="feb64-177">詳細については、「<xref:security/authorization/roles>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="feb64-178">ポリシーベースの承認の場合は、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="feb64-179">要求ベースの承認は、ポリシーベースの承認の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="feb64-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="feb64-180">たとえば、ユーザーが特定の要求持つことを必須にするポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="feb64-181">詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="feb64-182">これらの API は、Blazor Server アプリまたは Blazor WebAssembly アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="feb64-183"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> も <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> も指定されていない場合、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> には既定のポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="feb64-184">非同期認証中に表示されるコンテンツ</span><span class="sxs-lookup"><span data-stu-id="feb64-184">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="feb64-185"> では、認証状態を "*非同期的に*" 決定することができます。</span><span class="sxs-lookup"><span data-stu-id="feb64-185"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="feb64-186">このアプローチの主なシナリオは、認証のために外部エンドポイントに要求を送信する Blazor WebAssembly アプリです。</span><span class="sxs-lookup"><span data-stu-id="feb64-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="feb64-187">認証が進行中の間、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> には既定でコンテンツが表示されません。</span><span class="sxs-lookup"><span data-stu-id="feb64-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="feb64-188">認証が行われている間にコンテンツを表示するには、`<Authorizing>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="feb64-189">通常、このアプローチは Blazor Server アプリには適用されません。</span><span class="sxs-lookup"><span data-stu-id="feb64-189">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor Server<span data-ttu-id="feb64-190"> アプリでは、状態が確立されるとすぐに認証状態が認識されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-190"> apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="feb64-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> コンテンツは Blazor Server アプリの <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> コンポーネントで提供できますが、コンテンツは表示されません。</span><span class="sxs-lookup"><span data-stu-id="feb64-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="feb64-192">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="feb64-192">[Authorize] attribute</span></span>

<span data-ttu-id="feb64-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は Razor コンポーネント内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="feb64-194">Blazor ルーター経由で到達した `@page` コンポーネントにのみ [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) を使用してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="feb64-195">承認はルーティングの一面としてのみ実行され、ページ内にレンダリングされた子コンポーネントに対しては実行され "*ません*"。</span><span class="sxs-lookup"><span data-stu-id="feb64-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="feb64-196">ページ内の特定部分の表示を承認するには、代わりに <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> を使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="feb64-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、ロールベースまたはポリシーベースの承認もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="feb64-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="feb64-198">ロールベースの承認の場合は、<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="feb64-199">ポリシーベースの承認の場合は、<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="feb64-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="feb64-200"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> も <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> も指定されていない場合、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) には既定のポリシーが使用されます。これは既定で以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="feb64-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="feb64-201">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="feb64-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="feb64-202">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="feb64-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="feb64-203">Router コンポーネントを使用して承認されていないコンテンツをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="feb64-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="feb64-204"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> コンポーネントとともに使用すると、以下の場合にアプリがカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="feb64-205">コンテンツが見つからない。</span><span class="sxs-lookup"><span data-stu-id="feb64-205">Content isn't found.</span></span>
* <span data-ttu-id="feb64-206">ユーザーはコンポーネントに適用されている [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 条件に失敗します。</span><span class="sxs-lookup"><span data-stu-id="feb64-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="feb64-207">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性については、「[`[Authorize]` 属性](#authorize-attribute)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="feb64-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="feb64-208">非同期認証が実行中です。</span><span class="sxs-lookup"><span data-stu-id="feb64-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="feb64-209">既定の Blazor Server プロジェクト テンプレートでは、`App` コンポーネント (`App.razor`) によりカスタム コンテンツの設定方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="feb64-210">`<NotFound>`、`<NotAuthorized>`、および `<Authorizing>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="feb64-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="feb64-211">`<NotAuthorized>` 要素が指定されていない場合、<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> には次のフォールバック メッセージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="feb64-212">認証状態の変更に関する通知</span><span class="sxs-lookup"><span data-stu-id="feb64-212">Notification about authentication state changes</span></span>

<span data-ttu-id="feb64-213">基となる認証状態データが変わったとアプリに判断された場合 (たとえば、ユーザーがサインアウトした、または別のユーザーがロールを変更したなど)、[カスタムの `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) では必要に応じて <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 基底クラスのメソッド <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="feb64-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="feb64-214">これにより、新しいデータを使用して再表示するように、認証状態データ (<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> など) がコンシューマーに通知されます。</span><span class="sxs-lookup"><span data-stu-id="feb64-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="feb64-215">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="feb64-215">Procedural logic</span></span>

<span data-ttu-id="feb64-216">手続き型ロジックの一部としてアプリで承認規則をチェックする必要がある場合、型 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` のカスケード パラメーターを使用してユーザーの <xref:System.Security.Claims.ClaimsPrincipal> を取得します。</span><span class="sxs-lookup"><span data-stu-id="feb64-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="feb64-217">ポリシーを評価するために、`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` を `IAuthorizationService` などの他のサービスと組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="feb64-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="feb64-218">Blazor WebAssembly アプリ コンポーネントで、<xref:Microsoft.AspNetCore.Authorization> と <xref:Microsoft.AspNetCore.Components.Authorization> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="feb64-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="feb64-219">これらの名前空間は、アプリの `_Imports.razor` ファイルに追加することで、グローバルに提供できます。</span><span class="sxs-lookup"><span data-stu-id="feb64-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="feb64-220">エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="feb64-220">Troubleshoot errors</span></span>

<span data-ttu-id="feb64-221">一般的なエラー:</span><span class="sxs-lookup"><span data-stu-id="feb64-221">Common errors:</span></span>

* <span data-ttu-id="feb64-222">**承認には、型 `Task\<AuthenticationState>` のカスケード パラメーターが必要です。これを実行するには `CascadingAuthenticationState` の使用を検討します。**</span><span class="sxs-lookup"><span data-stu-id="feb64-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="feb64-223">`authenticationStateTask` **に対して**`null` 値を受け取ります</span><span class="sxs-lookup"><span data-stu-id="feb64-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="feb64-224">認証が有効な Blazor Server テンプレートを使用してプロジェクトが作成されなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="feb64-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="feb64-225">UI ツリーの一部に `<CascadingAuthenticationState>` をラップします。たとえば、`App` コンポーネント (`App.razor`) で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="feb64-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="feb64-226"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> には `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` カスケード パラメーターが用意されており、次に基となる <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI サービスから受け取ります。</span><span class="sxs-lookup"><span data-stu-id="feb64-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="feb64-227">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="feb64-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="feb64-228">[すばらしい Blazor: 認証](https://github.com/AdrienTorris/awesome-blazor#authentication) コミュニティのサンプル リンク</span><span class="sxs-lookup"><span data-stu-id="feb64-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
