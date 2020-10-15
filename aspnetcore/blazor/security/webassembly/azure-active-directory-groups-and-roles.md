---
title: Azure Active Directory のグループとロールを使用する ASP.NET Core Blazor WebAssembly
author: guardrex
description: Azure Active Directory のグループとロールを使用するように Blazor WebAssembly を構成する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900701"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="1a87b-103">Azure Active Directory (AAD) グループ、管理者ロール、およびユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="1a87b-104">作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1a87b-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="1a87b-105">Azure Active Directory (AAD) には、ASP.NET Core Identity と組み合わせることができる承認方法がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1a87b-106">ユーザー定義グループ</span><span class="sxs-lookup"><span data-stu-id="1a87b-106">User-defined groups</span></span>
  * <span data-ttu-id="1a87b-107">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="1a87b-107">Security</span></span>
  * <span data-ttu-id="1a87b-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="1a87b-108">Microsoft 365</span></span>
  * <span data-ttu-id="1a87b-109">配布</span><span class="sxs-lookup"><span data-stu-id="1a87b-109">Distribution</span></span>
* <span data-ttu-id="1a87b-110">役割</span><span class="sxs-lookup"><span data-stu-id="1a87b-110">Roles</span></span>
  * <span data-ttu-id="1a87b-111">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="1a87b-112">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-112">User-defined roles</span></span>

<span data-ttu-id="1a87b-113">この記事のガイダンスは、次のトピックで説明されている Blazor WebAssembly AAD デプロイ シナリオに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="1a87b-114">Microsoft アカウントによるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="1a87b-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1a87b-115">AAD によるスタンドアロン</span><span class="sxs-lookup"><span data-stu-id="1a87b-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1a87b-116">AAD によるホスティング</span><span class="sxs-lookup"><span data-stu-id="1a87b-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="1a87b-117">Microsoft Graph API のアクセス許可</span><span class="sxs-lookup"><span data-stu-id="1a87b-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="1a87b-118">6 つ以上の AAD 管理者ロールおよびセキュリティ グループ メンバーシップを持つアプリ ユーザーの場合は、[Microsoft Graph API](/graph/use-the-api) の呼び出しが必須です。</span><span class="sxs-lookup"><span data-stu-id="1a87b-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="1a87b-119">Graph API の呼び出しを許可するには、Azure portal で次の [Graph API アクセス許可](/graph/permissions-reference)を、ホストされている Blazor ソリューションのスタンドアロンまたは *`Client`* アプリに付与します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="1a87b-120">`Directory.Read.All` は、最小特権アクセス許可であり、この記事で説明する例に使用されているアクセス許可です。</span><span class="sxs-lookup"><span data-stu-id="1a87b-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="1a87b-121">ユーザー定義グループと管理者ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="1a87b-122">`groups` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="1a87b-123">ユーザー定義 AAD グループと AAD 管理者ロールにユーザーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="1a87b-124">Azure AD セキュリティ グループを使用したロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="1a87b-125">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="1a87b-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="1a87b-126">次の例では、ユーザーが AAD の "*課金管理者*" ロールに割り当てられているものとします。</span><span class="sxs-lookup"><span data-stu-id="1a87b-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="1a87b-127">AAD によって送信される単一の `groups` 要求では、ユーザーのグループとロールが JSON 配列内のオブジェクト ID (GUID) として示されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="1a87b-128">アプリでは、グループとロールの JSON 配列を、アプリで[ポリシー](xref:security/authorization/policies)を作成できる個々の `group` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a87b-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="1a87b-129">割り当てられている AAD 管理者ロールとユーザー定義グループの数が 5 つを超えると、`groups` 要求を送信するのではなく、`true` 値を持つ `hasgroups` 要求が AAD から送信されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="1a87b-130">ユーザーに割り当てられているロールとグループが 5 つを超える可能性があるアプリでは、ユーザーのロールとグループを取得するために、個別の Graph API 呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a87b-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="1a87b-131">この記事で提供している実装例は、このシナリオに対処するものです。</span><span class="sxs-lookup"><span data-stu-id="1a87b-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="1a87b-132">詳細については、`groups` および `hasgroups` 要求の情報について [Microsoft ID プラットフォーム アクセス トークン: ペイロードの要求](/azure/active-directory/develop/access-tokens#payload-claims)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="1a87b-133"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して、グループとロールの配列プロパティを含めます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="1a87b-134">各プロパティには空の配列を割り当てます。これにより、これらのプロパティを後で `foreach` ループ内で使用する場合に `null` を確認しなくても済むようになります。</span><span class="sxs-lookup"><span data-stu-id="1a87b-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="1a87b-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="1a87b-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="1a87b-136">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリで、カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-136">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="1a87b-137">ロールとグループの情報を取得する Graph API 呼び出しには、正しいスコープ (アクセス許可) を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="1a87b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="1a87b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="1a87b-139">`Program.Main` (`Program.cs`) では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 実装サービスを追加し、さらに Graph API 要求を行うための名前付き <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="1a87b-140">次の例では、クライアントに `GraphAPI` という名前を付けています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="1a87b-141">Graph API 呼び出しから Open Data Protocol (OData) ロールおよびグループを受信するように AAD ディレクトリ オブジェクト クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="1a87b-142">OData は JSON 形式で届きます。<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> の呼び出しによって `DirectoryObjects` クラスのインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="1a87b-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="1a87b-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="1a87b-144">ロールとグループの要求を処理するカスタム ユーザー ファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="1a87b-145">次の実装例では、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明する `roles` 要求の配列も処理します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="1a87b-146">`hasgroups` 要求が存在する場合は、名前付きの <xref:System.Net.Http.HttpClient> を使用して、ユーザーのロールとグループを取得するために Graph API に承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="1a87b-147">この実装では、Microsoft Identity Platform v1.0 エンドポイント `https://graph.microsoft.com/v1.0/me/memberOf` ([API ドキュメント](/graph/api/user-list-memberof)) を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="1a87b-148">このトピックのガイダンスが Identity v2.0 用に更新されるのは、MSAL パッケージが v2.0 用にアップグレードされたときです。</span><span class="sxs-lookup"><span data-stu-id="1a87b-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="1a87b-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="1a87b-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
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
                    var client = clientFactory.CreateClient("GraphAPI");

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
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="1a87b-150">元の `groups` 要求は (存在する場合) フレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="1a87b-151">この例のアプローチは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1a87b-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="1a87b-152">カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを追加して、アクセス トークンを送信要求にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="1a87b-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="1a87b-153">セキュリティで保護された外部 Web API エンドポイントに Web API 要求を送信するために、名前付きの <xref:System.Net.Http.HttpClient> を追加します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="1a87b-154">名前付きの <xref:System.Net.Http.HttpClient> を使用して、承認された要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="1a87b-155">このアプローチの一般的な対象範囲については、記事 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="1a87b-156">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリの `Program.Main` (`Program.cs`) に、ファクトリを登録します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="1a87b-157">アプリの追加のスコープとして `Directory.Read.All` アクセス許可スコープに同意します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="1a87b-158">`Program.Main` で、グループごとまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="1a87b-159">次の例では、AAD の "*課金管理者*" ロールに対するポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1a87b-160">AAD ロール オブジェクト ID の完全な一覧については、「[AAD 管理者ロールのオブジェクト ID](#aad-administrator-role-object-ids)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="1a87b-161">次の例のアプリでは、前述のポリシーを使用してユーザーを承認します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="1a87b-162">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
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

<span data-ttu-id="1a87b-163">コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="1a87b-164">ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="1a87b-165">ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="1a87b-166">ユーザー定義グループと管理者ロールに対してサーバー API アクセスを承認する</span><span class="sxs-lookup"><span data-stu-id="1a87b-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="1a87b-167">クライアント側 WebAssembly のユーザーにページとリソースへのアクセスを承認するだけでなく、サーバー API でユーザーにセキュリティで保護された API エンドポイントへのアクセスを承認することができます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="1a87b-168">*Server* アプリによってユーザーのアクセス トークンが検証された後、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1a87b-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="1a87b-169">アプリでは、JWT (`id_token`) からのユーザーの変更不可能な[オブジェクト識別子要求 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) を使用して、Graph API のアクセス トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="1a87b-170">Graph API の呼び出しによって、ユーザーの Azure ユーザー定義セキュリティ グループと管理者ロールのメンバーシップが取得されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="1a87b-171">メンバーシップは `group` 要求を確立するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="1a87b-172">[認証ポリシー](xref:security/authorization/policies)を使用すると、サーバー API エンドポイントへのユーザー アクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="1a87b-173">現在、このガイドには、ユーザーの [AAD ユーザー定義ロール](#user-defined-roles)に基づく承認は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="1a87b-174">パッケージ</span><span class="sxs-lookup"><span data-stu-id="1a87b-174">Packages</span></span>

<span data-ttu-id="1a87b-175">次のパッケージの *Server* アプリにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="1a87b-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="1a87b-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="1a87b-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="1a87b-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="1a87b-178">Azure の構成</span><span class="sxs-lookup"><span data-stu-id="1a87b-178">Azure configuration</span></span>

* <span data-ttu-id="1a87b-179">*Server* アプリの登録に、`Directory.Read.All` の Graph API アクセス許可への API アクセスが付与されていることを確認します。これはセキュリティ グループの最小特権アクセス レベルです。</span><span class="sxs-lookup"><span data-stu-id="1a87b-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="1a87b-180">アクセス許可の割り当てを行った後、アクセス許可に管理者の同意が適用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="1a87b-181">新しいクライアント シークレットを *Server* アプリに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="1a87b-182">「[アプリケーション設定](#app-settings)」セクションでのアプリの構成のために、シークレットをメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="1a87b-183">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="1a87b-183">App settings</span></span>

<span data-ttu-id="1a87b-184">アプリ設定ファイル (`appsettings.json` または `appsettings.Production.json`) に、Azure portal からの *Server* アプリのクライアント シークレットを持つ `ClientSecret` エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="1a87b-185">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="1a87b-186">承認ポリシー</span><span class="sxs-lookup"><span data-stu-id="1a87b-186">Authorization policies</span></span>

<span data-ttu-id="1a87b-187">グループ オブジェクト ID と [AAD 管理者ロール オブジェクト ID](#aad-administrator-role-object-ids) に基づいて、*Server* アプリの `Startup.ConfigureServices` (`Startup.cs`) で、AAD セキュリティ グループと AAD 管理者ロールに対する[承認ポリシー](xref:security/authorization/policies)を作成します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="1a87b-188">たとえば、Azure の課金管理者ロール ポリシーには、次の構成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1a87b-189">詳細については、<xref:security/authorization/policies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="1a87b-190">コントローラーのアクセス</span><span class="sxs-lookup"><span data-stu-id="1a87b-190">Controller access</span></span>

<span data-ttu-id="1a87b-191">*Server* アプリのコントローラーに対するポリシーが必要です。</span><span class="sxs-lookup"><span data-stu-id="1a87b-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="1a87b-192">次の例では、`BillingDataController` からの課金データへのアクセスを、「[承認ポリシー](#authorization-policies)」セクションで構成したように、`BillingAdmin` というポリシー名を持つ Azure 課金管理者に制限しています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="1a87b-193">サービス構成</span><span class="sxs-lookup"><span data-stu-id="1a87b-193">Service configuration</span></span>

<span data-ttu-id="1a87b-194">*Server* アプリの `Startup.ConfigureServices` メソッドに、Graph API 呼び出しを行い、ユーザーのセキュリティ グループとロールに対するユーザー `group` 要求を確立するためのロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="1a87b-195">このセクションのコード例では、Microsoft Identity Platform v1.0 に基づく Active Directory 認証ライブラリ (ADAL) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="1a87b-196">このトピックは、Identity v2.0 for .NET 5 に対して更新されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="1a87b-197">この作業の進行状況を追跡するには、[「[RC1] Microsoft Identity Platform 2.0 for Blazor」(dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503) を監視してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="1a87b-198">*Server* アプリの `Startup` クラスのコードには、追加の名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="1a87b-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="1a87b-199">次の一連の `using` ステートメントには、このセクションで後述するコードに必要な名前空間が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="1a87b-200"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> を構成する場合:</span><span class="sxs-lookup"><span data-stu-id="1a87b-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="1a87b-201">必要に応じて <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> の処理を含めます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1a87b-202">たとえば、アプリでは失敗した認証をログに記録できます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="1a87b-203"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> で、Graph API 呼び出しを行ってユーザーのグループとロールを取得します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="1a87b-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> によって、メッセージのログ記録に個人を特定できる情報 (PII) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="1a87b-205">PII は、テスト ユーザー アカウントを使用したデバッグに対してのみアクティブにしてください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="1a87b-206">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1a87b-206">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="1a87b-207">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="1a87b-207">In the preceding example:</span></span>

* <span data-ttu-id="1a87b-208">アクセス トークンが既に ADAL トークン キャッシュに格納されている可能性があるため、最初にサイレント トークンの取得 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) が試行されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="1a87b-209">キャッシュからトークンを取得する方が、新しいトークンを要求するよりも高速です。</span><span class="sxs-lookup"><span data-stu-id="1a87b-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="1a87b-210">アクセス トークンがキャッシュから取得されない場合 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> または <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> がスローされた場合) は、クライアント資格情報 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) を使用してユーザー アサーション (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) を作成し、ユーザーの代わりにトークンを取得します (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>)。</span><span class="sxs-lookup"><span data-stu-id="1a87b-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="1a87b-211">次に、`Microsoft.Graph.GraphServiceClient` により、トークンを使用して Graph API 呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="1a87b-212">トークンは ADAL トークン キャッシュに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="1a87b-213">その後同じユーザーに対して Graph API を呼び出す場合、トークンは <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> を使用してキャッシュからサイレントに取得されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="1a87b-214"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> のコードでは、推移的なメンバーシップが取得されません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="1a87b-215">直接的および推移的なメンバーシップを取得するようにコードを変更するには:</span><span class="sxs-lookup"><span data-stu-id="1a87b-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="1a87b-216">次のコード行に対して:</span><span class="sxs-lookup"><span data-stu-id="1a87b-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="1a87b-217">前の行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="1a87b-218">次のコード行に対して:</span><span class="sxs-lookup"><span data-stu-id="1a87b-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="1a87b-219">前の行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="1a87b-220"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> のコードでは、要求の作成時に AAD セキュリティ グループと AAD 管理者ロールが区別されません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="1a87b-221">アプリでグループとロールを区別するには、グループとロールを反復処理するときに `entry.ODataType` をチェックします。</span><span class="sxs-lookup"><span data-stu-id="1a87b-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="1a87b-222">セキュリティ グループとロールの要求を別々に作成するには、次のようなコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-222">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="1a87b-223">ユーザー定義ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-223">User-defined roles</span></span>

<span data-ttu-id="1a87b-224">ユーザー定義ロールを使用するように、AAD 登録済みアプリを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="1a87b-225">`roles` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="1a87b-226">以下の例では、次の 2 つのロールがアプリに構成されているものとします。</span><span class="sxs-lookup"><span data-stu-id="1a87b-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="1a87b-227">Azure AD Premium アカウントがないとセキュリティ グループにロールを割り当てることはできませんが、ユーザーをロールに割り当てて、Standard Azure アカウントを使用するユーザーに対する `roles` 要求を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="1a87b-228">このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="1a87b-229">Azure portal で各追加ロール割り当てに対して **_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="1a87b-230">AAD によって送信される単一の `roles` 要求では、ユーザー定義のロールは JSON 配列内の `appRoles` の `value` として示されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="1a87b-231">アプリでは、ロールの JSON 配列を個々の `role` 要求に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a87b-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="1a87b-232">[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションで示されている `CustomUserFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="1a87b-233">[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションで示されているように、ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリに `CustomUserFactory` を追加して登録します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-233">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="1a87b-234">元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1a87b-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1a87b-235">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリの `Program.Main` で、ロール要求として "`role`" という名前の要求を指定します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-235">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="1a87b-236">この時点でコンポーネントの承認方法が機能しています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="1a87b-237">コンポーネント内のすべての承認メカニズムで、`admin` ロールを使用してユーザーを承認できます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="1a87b-238">[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component) (例: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="1a87b-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="1a87b-239">[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (例: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="1a87b-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="1a87b-240">[手続き型ロジック](xref:blazor/security/index#procedural-logic) (例: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="1a87b-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="1a87b-241">複数のロール テストがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1a87b-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="1a87b-242">AAD 管理者ロールのオブジェクト ID</span><span class="sxs-lookup"><span data-stu-id="1a87b-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="1a87b-243">次の表で示されているオブジェクト ID は、`group` 要求に対する[ポリシー](xref:security/authorization/policies)を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1a87b-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="1a87b-244">ポリシーでは、アプリ内のさまざまなアクティビティについてユーザーを承認することをアプリに許可します。</span><span class="sxs-lookup"><span data-stu-id="1a87b-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="1a87b-245">詳細については、[ユーザー定義グループと AAD 管理者ロール](#user-defined-groups-and-administrator-roles)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a87b-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="1a87b-246">AAD 管理者ロール</span><span class="sxs-lookup"><span data-stu-id="1a87b-246">AAD Administrator Role</span></span> | <span data-ttu-id="1a87b-247">Object ID</span><span class="sxs-lookup"><span data-stu-id="1a87b-247">Object ID</span></span>
--- | ---
<span data-ttu-id="1a87b-248">アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-248">Application administrator</span></span> | <span data-ttu-id="1a87b-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="1a87b-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="1a87b-250">アプリケーション開発者</span><span class="sxs-lookup"><span data-stu-id="1a87b-250">Application developer</span></span> | <span data-ttu-id="1a87b-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="1a87b-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="1a87b-252">認証管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-252">Authentication administrator</span></span> | <span data-ttu-id="1a87b-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="1a87b-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="1a87b-254">Azure DevOps 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-254">Azure DevOps administrator</span></span> | <span data-ttu-id="1a87b-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="1a87b-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="1a87b-256">Azure Information Protection 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="1a87b-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="1a87b-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="1a87b-258">B2C IEF キーセット管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="1a87b-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="1a87b-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="1a87b-260">B2C IEF ポリシー管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="1a87b-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="1a87b-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="1a87b-262">B2C ユーザー フロー管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-262">B2C user flow administrator</span></span> | <span data-ttu-id="1a87b-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="1a87b-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="1a87b-264">B2C ユーザー フロー属性管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="1a87b-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="1a87b-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="1a87b-266">課金管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-266">Billing administrator</span></span> | <span data-ttu-id="1a87b-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="1a87b-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="1a87b-268">クラウド アプリケーション管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-268">Cloud application administrator</span></span> | <span data-ttu-id="1a87b-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="1a87b-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="1a87b-270">クラウド デバイス管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-270">Cloud device administrator</span></span> | <span data-ttu-id="1a87b-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="1a87b-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="1a87b-272">コンプライアンス管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-272">Compliance administrator</span></span> | <span data-ttu-id="1a87b-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="1a87b-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="1a87b-274">コンプライアンス データ管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-274">Compliance data administrator</span></span> | <span data-ttu-id="1a87b-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="1a87b-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="1a87b-276">条件付きアクセス管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-276">Conditional Access administrator</span></span> | <span data-ttu-id="1a87b-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="1a87b-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="1a87b-278">カスタマー ロックボックスのアクセス承認者</span><span class="sxs-lookup"><span data-stu-id="1a87b-278">Customer LockBox access approver</span></span> | <span data-ttu-id="1a87b-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="1a87b-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="1a87b-280">デスクトップ Analytics 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="1a87b-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="1a87b-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="1a87b-282">ディレクトリ閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-282">Directory readers</span></span> | <span data-ttu-id="1a87b-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="1a87b-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="1a87b-284">Dynamics 365 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="1a87b-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="1a87b-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="1a87b-286">Exchange 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-286">Exchange administrator</span></span> | <span data-ttu-id="1a87b-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="1a87b-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="1a87b-288">外部 IdentityID プロバイダー管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-288">External Identity Provider administrator</span></span> | <span data-ttu-id="1a87b-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="1a87b-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="1a87b-290">全体管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-290">Global administrator</span></span> | <span data-ttu-id="1a87b-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="1a87b-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="1a87b-292">グローバル閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-292">Global reader</span></span> | <span data-ttu-id="1a87b-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="1a87b-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="1a87b-294">グループ管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-294">Groups administrator</span></span> | <span data-ttu-id="1a87b-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="1a87b-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="1a87b-296">ゲスト招待元</span><span class="sxs-lookup"><span data-stu-id="1a87b-296">Guest inviter</span></span> | <span data-ttu-id="1a87b-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="1a87b-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="1a87b-298">ヘルプデスク管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-298">Helpdesk administrator</span></span> | <span data-ttu-id="1a87b-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="1a87b-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="1a87b-300">Intune 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-300">Intune administrator</span></span> | <span data-ttu-id="1a87b-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="1a87b-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="1a87b-302">Kaizala 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-302">Kaizala administrator</span></span> | <span data-ttu-id="1a87b-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="1a87b-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="1a87b-304">ライセンス管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-304">License administrator</span></span> | <span data-ttu-id="1a87b-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="1a87b-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="1a87b-306">メッセージ センターのプライバシー閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-306">Message center privacy reader</span></span> | <span data-ttu-id="1a87b-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="1a87b-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="1a87b-308">メッセージ センター閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-308">Message center reader</span></span> | <span data-ttu-id="1a87b-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="1a87b-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="1a87b-310">Office アプリ管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-310">Office apps administrator</span></span> | <span data-ttu-id="1a87b-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="1a87b-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="1a87b-312">パスワード管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-312">Password administrator</span></span> | <span data-ttu-id="1a87b-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="1a87b-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="1a87b-314">Power BI 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-314">Power BI administrator</span></span> | <span data-ttu-id="1a87b-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="1a87b-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="1a87b-316">Power Platform 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-316">Power platform administrator</span></span> | <span data-ttu-id="1a87b-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="1a87b-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="1a87b-318">特権認証管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-318">Privileged authentication administrator</span></span> | <span data-ttu-id="1a87b-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="1a87b-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="1a87b-320">特権ロール管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-320">Privileged role administrator</span></span> | <span data-ttu-id="1a87b-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="1a87b-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="1a87b-322">レポート閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-322">Reports reader</span></span> | <span data-ttu-id="1a87b-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="1a87b-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="1a87b-324">Search 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-324">Search administrator</span></span> | <span data-ttu-id="1a87b-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="1a87b-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="1a87b-326">Search エディター</span><span class="sxs-lookup"><span data-stu-id="1a87b-326">Search editor</span></span> | <span data-ttu-id="1a87b-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="1a87b-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="1a87b-328">セキュリティ管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-328">Security administrator</span></span> | <span data-ttu-id="1a87b-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="1a87b-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="1a87b-330">セキュリティ オペレーター</span><span class="sxs-lookup"><span data-stu-id="1a87b-330">Security operator</span></span> | <span data-ttu-id="1a87b-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="1a87b-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="1a87b-332">セキュリティ閲覧者</span><span class="sxs-lookup"><span data-stu-id="1a87b-332">Security reader</span></span> | <span data-ttu-id="1a87b-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="1a87b-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="1a87b-334">サービス サポート管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-334">Service support administrator</span></span> | <span data-ttu-id="1a87b-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="1a87b-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="1a87b-336">SharePoint 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-336">SharePoint administrator</span></span> | <span data-ttu-id="1a87b-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="1a87b-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="1a87b-338">Skype for Business 管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-338">Skype for Business administrator</span></span> | <span data-ttu-id="1a87b-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="1a87b-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="1a87b-340">Teams 通信管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-340">Teams Communications Administrator</span></span> | <span data-ttu-id="1a87b-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="1a87b-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="1a87b-342">Teams 通信サポート エンジニア</span><span class="sxs-lookup"><span data-stu-id="1a87b-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="1a87b-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="1a87b-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="1a87b-344">Teams 通信スペシャリスト</span><span class="sxs-lookup"><span data-stu-id="1a87b-344">Teams Communications Specialist</span></span> | <span data-ttu-id="1a87b-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="1a87b-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="1a87b-346">Teams サービス管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-346">Teams Service Administrator</span></span> | <span data-ttu-id="1a87b-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="1a87b-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="1a87b-348">ユーザー管理者</span><span class="sxs-lookup"><span data-stu-id="1a87b-348">User administrator</span></span> | <span data-ttu-id="1a87b-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="1a87b-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a87b-350">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1a87b-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
