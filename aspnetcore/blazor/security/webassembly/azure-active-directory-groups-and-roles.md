---
title: Azure Active Directory のグループとロールを使用する ASP.NET Core Blazor WebAssembly
author: guardrex
description: Azure Active Directory のグループとロールを使用するように Blazor WebAssembly を構成する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: bd0a7c5a905ae4888ea6ad326be5b16cbfaa10fc
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130406"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="da14b-103">Azure AD のグループ、管理者ロール、ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="da14b-104">作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="da14b-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="da14b-105">Azure Active Directory (AAD) には、ASP.NET Core Identity と組み合わせることができる承認方法がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="da14b-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="da14b-106">ユーザー定義グループ</span><span class="sxs-lookup"><span data-stu-id="da14b-106">User-defined groups</span></span>
  * <span data-ttu-id="da14b-107">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="da14b-107">Security</span></span>
  * <span data-ttu-id="da14b-108">O365</span><span class="sxs-lookup"><span data-stu-id="da14b-108">O365</span></span>
  * <span data-ttu-id="da14b-109">配布</span><span class="sxs-lookup"><span data-stu-id="da14b-109">Distribution</span></span>
* <span data-ttu-id="da14b-110">役割</span><span class="sxs-lookup"><span data-stu-id="da14b-110">Roles</span></span>
  * <span data-ttu-id="da14b-111">組み込み管理者ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="da14b-112">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-112">User-defined roles</span></span>

<span data-ttu-id="da14b-113">この記事のガイダンスは、次のトピックで説明されている Blazor WebAssembly AAD デプロイ シナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="da14b-114">Microsoft アカウントによるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="da14b-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="da14b-115">AAD によるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="da14b-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="da14b-116">AAD によるホスティング</span><span class="sxs-lookup"><span data-stu-id="da14b-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="da14b-117">Microsoft Graph API のアクセス許可</span><span class="sxs-lookup"><span data-stu-id="da14b-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="da14b-118">6 つ以上の組み込みの AAD 管理者ロールおよびセキュリティ グループ メンバーシップを持つアプリ ユーザーの場合は、[Microsoft Graph API](/graph/use-the-api) の呼び出しが必須です。</span><span class="sxs-lookup"><span data-stu-id="da14b-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="da14b-119">Graph API の呼び出しを許可するには、Azure portal で次の [Graph API アクセス許可](/graph/permissions-reference)を、ホストされている Blazor ソリューションのスタンドアロンまたはクライアント アプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="da14b-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="da14b-120">`Directory.Read.All` は、最小特権アクセス許可であり、この記事で説明する例に使用されているアクセス許可です。</span><span class="sxs-lookup"><span data-stu-id="da14b-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="da14b-121">ユーザー定義グループと組み込み管理者ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="da14b-122">`groups` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="da14b-123">ユーザー定義 AAD グループと組み込み管理者ロールにユーザーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="da14b-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="da14b-124">Azure AD セキュリティ グループを使用したロール</span><span class="sxs-lookup"><span data-stu-id="da14b-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="da14b-125">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="da14b-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="da14b-126">次の例では、ユーザーは AAD の組み込み "*課金管理者*" ロールに割り当てられているものとします。</span><span class="sxs-lookup"><span data-stu-id="da14b-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="da14b-127">AAD によって送信される単一の `groups` 要求では、ユーザーのグループとロールが JSON 配列内のオブジェクト ID (GUID) として示されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="da14b-128">アプリでは、グループとロールの JSON 配列を、アプリで[ポリシー](xref:security/authorization/policies)を作成できる個々の `group` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da14b-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="da14b-129">割り当てられている組み込みの Azure 管理者ロールとユーザー定義グループの数が 5 つを超えると、`groups` 要求を送信するのではなく、`true` 値を持つ `hasgroups` 要求が AAD から送信されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="da14b-130">ユーザーに割り当てられているロールとグループが 5 つを超える可能性があるアプリでは、ユーザーのロールとグループを取得するために、個別の Graph API 呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="da14b-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="da14b-131">この記事で提供している実装例は、このシナリオに対処するものです。</span><span class="sxs-lookup"><span data-stu-id="da14b-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="da14b-132">詳細については、`groups` および `hasgroups` 要求の情報について [Microsoft ID プラットフォーム アクセス トークン: ペイロードの要求](/azure/active-directory/develop/access-tokens#payload-claims)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="da14b-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して、グループとロールの配列プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="da14b-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="da14b-134">各プロパティには空の配列を割り当てます。これにより、これらのプロパティを後で `foreach` ループ内で使用する場合に `null` を確認しなくても済むようになります。</span><span class="sxs-lookup"><span data-stu-id="da14b-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="da14b-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="da14b-135">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

<span data-ttu-id="da14b-136">ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリで、カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="da14b-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="da14b-137">ロールとグループの情報を取得する Graph API 呼び出しには、正しいスコープ (アクセス許可) を使用します。</span><span class="sxs-lookup"><span data-stu-id="da14b-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="da14b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="da14b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="da14b-139">`Program.Main` (`Program.cs`) では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 実装サービスを追加し、さらに Graph API 要求を行うための名前付き <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="da14b-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="da14b-140">次の例では、クライアントに `GraphAPI` という名前を付けています。</span><span class="sxs-lookup"><span data-stu-id="da14b-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="da14b-141">Graph API 呼び出しから Open Data Protocol (OData) ロールおよびグループを受信するように AAD ディレクトリ オブジェクト クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="da14b-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="da14b-142">OData は JSON 形式で届きます。<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> の呼び出しによって `DirectoryObjects` クラスのインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="da14b-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="da14b-143">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="da14b-144">ロールとグループの要求を処理するカスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="da14b-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="da14b-145">次の実装例では、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明する `roles` 要求の配列も処理します。</span><span class="sxs-lookup"><span data-stu-id="da14b-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="da14b-146">`hasgroups` 要求が存在する場合は、名前付きの <xref:System.Net.Http.HttpClient> を使用して、ユーザーのロールとグループを取得するために Graph API に承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="da14b-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="da14b-147">この実装では、Microsoft Identity Platform v1.0 エンドポイント `https://graph.microsoft.com/v1.0/me/memberOf` ([API ドキュメント](/graph/api/user-list-memberof)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="da14b-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="da14b-148">このトピックのガイダンスが Identity v2.0 用に更新されるのは、MSAL パッケージが v2.0 用にアップグレードされたときです。</span><span class="sxs-lookup"><span data-stu-id="da14b-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="da14b-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="da14b-149">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = _clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="da14b-150">元の `groups` 要求は (存在する場合) フレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="da14b-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="da14b-151">この例のアプローチは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="da14b-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="da14b-152">カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを追加して、アクセス トークンを送信要求にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="da14b-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="da14b-153">セキュリティで保護された外部 Web API エンドポイントに Web API 要求を送信するために、名前付きの <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="da14b-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="da14b-154">名前付きの <xref:System.Net.Http.HttpClient> を使用して、承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="da14b-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="da14b-155">このアプローチの一般的な対象範囲については、記事 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="da14b-156">ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリの `Program.Main` (`Program.cs`) に、ファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="da14b-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="da14b-157">アプリの追加のスコープとして `Directory.Read.All` アクセス許可スコープに同意します。</span><span class="sxs-lookup"><span data-stu-id="da14b-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="da14b-158">`Program.Main` で、グループごとまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="da14b-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="da14b-159">次の例では、AAD の組み込み "*課金管理者*" ロールに対するポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="da14b-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="da14b-160">AAD ロール オブジェクト ID の完全な一覧については、「[AAD の管理者ロールのグループ ID](#aad-adminstrative-role-group-ids)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="da14b-161">次の例のアプリでは、前述のポリシーを使用してユーザーを承認します。</span><span class="sxs-lookup"><span data-stu-id="da14b-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="da14b-162">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="da14b-163">コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。</span><span class="sxs-lookup"><span data-stu-id="da14b-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="da14b-164">ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="da14b-165">ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。</span><span class="sxs-lookup"><span data-stu-id="da14b-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="da14b-166">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-166">User-defined roles</span></span>

<span data-ttu-id="da14b-167">ユーザー定義ロールを使用するように、AAD 登録済みアプリを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="da14b-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="da14b-168">`roles` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="da14b-169">以下の例では、次の 2 つのロールがアプリに構成されているものとします。</span><span class="sxs-lookup"><span data-stu-id="da14b-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="da14b-170">Azure AD Premium アカウントがないとセキュリティ グループにロールを割り当てることはできませんが、ユーザーをロールに割り当てて、Standard Azure アカウントを使用するユーザーに対する `roles` 要求を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="da14b-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="da14b-171">このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="da14b-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="da14b-172">Azure portal で各追加ロール割り当てに対して**_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="da14b-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="da14b-173">AAD によって送信される単一の `roles` 要求では、ユーザー定義のロールは JSON 配列内の `appRoles` の `value` として示されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="da14b-174">アプリでは、ロールの JSON 配列を個々の `role` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="da14b-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="da14b-175">「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されている `CustomUserFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="da14b-176">「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されているように、ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリで `CustomUserFactory` を追加して登録します。</span><span class="sxs-lookup"><span data-stu-id="da14b-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="da14b-177">元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="da14b-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="da14b-178">ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリの `Program.Main` で、ロール要求として "`role`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="da14b-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="da14b-179">この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="da14b-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="da14b-180">コンポーネント内のすべての承認メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="da14b-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="da14b-181">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="da14b-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="da14b-182">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="da14b-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="da14b-183">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="da14b-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="da14b-184">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="da14b-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="da14b-185">AAD の管理者ロールのグループ ID</span><span class="sxs-lookup"><span data-stu-id="da14b-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="da14b-186">次の表で示されているオブジェクト ID は、`group` 要求に対する[ポリシー](xref:security/authorization/policies)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="da14b-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="da14b-187">ポリシーでは、アプリ内のさまざまなアクティビティについてユーザーを承認することをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="da14b-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="da14b-188">詳細については、「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="da14b-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="da14b-189">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="da14b-189">AAD Administrative Role</span></span> | <span data-ttu-id="da14b-190">Object ID</span><span class="sxs-lookup"><span data-stu-id="da14b-190">Object ID</span></span>
--- | ---
<span data-ttu-id="da14b-191">アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-191">Application administrator</span></span> | <span data-ttu-id="da14b-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="da14b-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="da14b-193">アプリケーション開発者</span><span class="sxs-lookup"><span data-stu-id="da14b-193">Application developer</span></span> | <span data-ttu-id="da14b-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="da14b-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="da14b-195">認証管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-195">Authentication administrator</span></span> | <span data-ttu-id="da14b-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="da14b-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="da14b-197">Azure DevOps 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-197">Azure DevOps administrator</span></span> | <span data-ttu-id="da14b-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="da14b-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="da14b-199">Azure Information Protection 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="da14b-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="da14b-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="da14b-201">B2C IEF キーセット管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="da14b-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="da14b-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="da14b-203">B2C IEF ポリシー管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="da14b-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="da14b-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="da14b-205">B2C ユーザー フロー管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-205">B2C user flow administrator</span></span> | <span data-ttu-id="da14b-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="da14b-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="da14b-207">B2C ユーザー フロー属性管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="da14b-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="da14b-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="da14b-209">課金管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-209">Billing administrator</span></span> | <span data-ttu-id="da14b-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="da14b-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="da14b-211">クラウド アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-211">Cloud application administrator</span></span> | <span data-ttu-id="da14b-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="da14b-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="da14b-213">クラウド デバイス管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-213">Cloud device administrator</span></span> | <span data-ttu-id="da14b-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="da14b-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="da14b-215">コンプライアンス管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-215">Compliance administrator</span></span> | <span data-ttu-id="da14b-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="da14b-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="da14b-217">コンプライアンス データ管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-217">Compliance data administrator</span></span> | <span data-ttu-id="da14b-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="da14b-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="da14b-219">条件付きアクセス管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-219">Conditional Access administrator</span></span> | <span data-ttu-id="da14b-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="da14b-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="da14b-221">カスタマー ロックボックスのアクセス承認者</span><span class="sxs-lookup"><span data-stu-id="da14b-221">Customer LockBox access approver</span></span> | <span data-ttu-id="da14b-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="da14b-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="da14b-223">デスクトップ Analytics 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="da14b-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="da14b-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="da14b-225">ディレクトリ閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-225">Directory readers</span></span> | <span data-ttu-id="da14b-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="da14b-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="da14b-227">Dynamics 365 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="da14b-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="da14b-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="da14b-229">Exchange 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-229">Exchange administrator</span></span> | <span data-ttu-id="da14b-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="da14b-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="da14b-231">外部 IdentityID プロバイダー管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-231">External Identity Provider administrator</span></span> | <span data-ttu-id="da14b-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="da14b-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="da14b-233">全体管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-233">Global administrator</span></span> | <span data-ttu-id="da14b-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="da14b-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="da14b-235">グローバル閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-235">Global reader</span></span> | <span data-ttu-id="da14b-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="da14b-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="da14b-237">グループ管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-237">Groups administrator</span></span> | <span data-ttu-id="da14b-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="da14b-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="da14b-239">ゲスト招待元</span><span class="sxs-lookup"><span data-stu-id="da14b-239">Guest inviter</span></span> | <span data-ttu-id="da14b-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="da14b-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="da14b-241">ヘルプデスク管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-241">Helpdesk administrator</span></span> | <span data-ttu-id="da14b-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="da14b-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="da14b-243">Intune 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-243">Intune administrator</span></span> | <span data-ttu-id="da14b-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="da14b-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="da14b-245">Kaizala 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-245">Kaizala administrator</span></span> | <span data-ttu-id="da14b-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="da14b-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="da14b-247">ライセンス管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-247">License administrator</span></span> | <span data-ttu-id="da14b-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="da14b-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="da14b-249">メッセージ センターのプライバシー閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-249">Message center privacy reader</span></span> | <span data-ttu-id="da14b-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="da14b-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="da14b-251">メッセージ センター閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-251">Message center reader</span></span> | <span data-ttu-id="da14b-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="da14b-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="da14b-253">Office アプリ管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-253">Office apps administrator</span></span> | <span data-ttu-id="da14b-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="da14b-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="da14b-255">パスワード管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-255">Password administrator</span></span> | <span data-ttu-id="da14b-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="da14b-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="da14b-257">Power BI 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-257">Power BI administrator</span></span> | <span data-ttu-id="da14b-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="da14b-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="da14b-259">Power Platform 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-259">Power platform administrator</span></span> | <span data-ttu-id="da14b-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="da14b-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="da14b-261">特権認証管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-261">Privileged authentication administrator</span></span> | <span data-ttu-id="da14b-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="da14b-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="da14b-263">特権ロール管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-263">Privileged role administrator</span></span> | <span data-ttu-id="da14b-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="da14b-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="da14b-265">レポート閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-265">Reports reader</span></span> | <span data-ttu-id="da14b-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="da14b-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="da14b-267">Search 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-267">Search administrator</span></span> | <span data-ttu-id="da14b-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="da14b-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="da14b-269">Search エディター</span><span class="sxs-lookup"><span data-stu-id="da14b-269">Search editor</span></span> | <span data-ttu-id="da14b-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="da14b-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="da14b-271">セキュリティ管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-271">Security administrator</span></span> | <span data-ttu-id="da14b-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="da14b-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="da14b-273">セキュリティ オペレーター</span><span class="sxs-lookup"><span data-stu-id="da14b-273">Security operator</span></span> | <span data-ttu-id="da14b-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="da14b-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="da14b-275">セキュリティ閲覧者</span><span class="sxs-lookup"><span data-stu-id="da14b-275">Security reader</span></span> | <span data-ttu-id="da14b-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="da14b-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="da14b-277">サービス サポート管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-277">Service support administrator</span></span> | <span data-ttu-id="da14b-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="da14b-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="da14b-279">SharePoint 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-279">SharePoint administrator</span></span> | <span data-ttu-id="da14b-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="da14b-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="da14b-281">Skype for Business 管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-281">Skype for Business administrator</span></span> | <span data-ttu-id="da14b-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="da14b-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="da14b-283">Teams 通信管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-283">Teams Communications Administrator</span></span> | <span data-ttu-id="da14b-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="da14b-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="da14b-285">Teams 通信サポート エンジニア</span><span class="sxs-lookup"><span data-stu-id="da14b-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="da14b-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="da14b-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="da14b-287">Teams 通信スペシャリスト</span><span class="sxs-lookup"><span data-stu-id="da14b-287">Teams Communications Specialist</span></span> | <span data-ttu-id="da14b-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="da14b-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="da14b-289">Teams サービス管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-289">Teams Service Administrator</span></span> | <span data-ttu-id="da14b-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="da14b-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="da14b-291">ユーザー管理者</span><span class="sxs-lookup"><span data-stu-id="da14b-291">User administrator</span></span> | <span data-ttu-id="da14b-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="da14b-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da14b-293">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="da14b-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
