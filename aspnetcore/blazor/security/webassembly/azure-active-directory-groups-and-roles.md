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
ms.openlocfilehash: adc16989e5b4e2e639896e5fe9562e42cb8ceeb4
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102745"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD のグループ、管理者ロール、ユーザー定義ロール

作成者: [Luke Latham](https://github.com/guardrex)、[Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) には、ASP.NET Core Identity と組み合わせることができる承認方法がいくつか用意されています。

* ユーザー定義グループ
  * セキュリティ
  * Microsoft 365
  * 配布
* 役割
  * 組み込み管理者ロール
  * ユーザー定義ロール

この記事のガイダンスは、次のトピックで説明されている Blazor WebAssembly AAD デプロイ シナリオに適用されます。

* [Microsoft アカウントによるスタンドアロン](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD によるスタンドアロン](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD によるホスティング](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph API のアクセス許可

6 つ以上の組み込みの AAD 管理者ロールおよびセキュリティ グループ メンバーシップを持つアプリ ユーザーの場合は、[Microsoft Graph API](/graph/use-the-api) の呼び出しが必須です。

Graph API の呼び出しを許可するには、Azure portal で次の [Graph API アクセス許可](/graph/permissions-reference)を、ホストされている Blazor ソリューションのスタンドアロンまたはクライアント アプリに付与します。

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` は、最小特権アクセス許可であり、この記事で説明する例に使用されているアクセス許可です。

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>ユーザー定義グループと組み込み管理者ロール

`groups` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。 ユーザー定義 AAD グループと組み込み管理者ロールにユーザーを割り当てます。

* [Azure AD セキュリティ グループを使用したロール](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

次の例では、ユーザーは AAD の組み込み "*課金管理者*" ロールに割り当てられているものとします。

AAD によって送信される単一の `groups` 要求では、ユーザーのグループとロールが JSON 配列内のオブジェクト ID (GUID) として示されます。 アプリでは、グループとロールの JSON 配列を、アプリで[ポリシー](xref:security/authorization/policies)を作成できる個々の `group` 要求に変換する必要があります。

割り当てられている組み込みの Azure 管理者ロールとユーザー定義グループの数が 5 つを超えると、`groups` 要求を送信するのではなく、`true` 値を持つ `hasgroups` 要求が AAD から送信されます。 ユーザーに割り当てられているロールとグループが 5 つを超える可能性があるアプリでは、ユーザーのロールとグループを取得するために、個別の Graph API 呼び出しを行う必要があります。 この記事で提供している実装例は、このシナリオに対処するものです。 詳細については、`groups` および `hasgroups` 要求の情報について [Microsoft ID プラットフォーム アクセス トークン: ペイロードの要求](/azure/active-directory/develop/access-tokens#payload-claims)に関する記事を参照してください。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張して、グループとロールの配列プロパティを含めます。 各プロパティには空の配列を割り当てます。これにより、これらのプロパティを後で `foreach` ループ内で使用する場合に `null` を確認しなくても済むようになります。

`CustomUserAccount.cs`:

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

ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリで、カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを作成します。 ロールとグループの情報を取得する Graph API 呼び出しには、正しいスコープ (アクセス許可) を使用します。

`GraphAPIAuthorizationMessageHandler.cs`:

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

`Program.Main` (`Program.cs`) では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 実装サービスを追加し、さらに Graph API 要求を行うための名前付き <xref:System.Net.Http.HttpClient> を追加します。 次の例では、クライアントに `GraphAPI` という名前を付けています。

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Graph API 呼び出しから Open Data Protocol (OData) ロールおよびグループを受信するように AAD ディレクトリ オブジェクト クラスを作成します。 OData は JSON 形式で届きます。<xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> の呼び出しによって `DirectoryObjects` クラスのインスタンスが作成されます。

`DirectoryObjects.cs`:

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

ロールとグループの要求を処理するカスタム ユーザー ファクトリを作成します。 次の実装例では、「[ユーザー定義ロール](#user-defined-roles)」セクションで説明する `roles` 要求の配列も処理します。 `hasgroups` 要求が存在する場合は、名前付きの <xref:System.Net.Http.HttpClient> を使用して、ユーザーのロールとグループを取得するために Graph API に承認された要求を送信します。 この実装では、Microsoft Identity Platform v1.0 エンドポイント `https://graph.microsoft.com/v1.0/me/memberOf` ([API ドキュメント](/graph/api/user-list-memberof)) を使用します。 このトピックのガイダンスが Identity v2.0 用に更新されるのは、MSAL パッケージが v2.0 用にアップグレードされたときです。

`CustomAccountFactory.cs`:

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

元の `groups` 要求は (存在する場合) フレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。

> [!NOTE]
> この例のアプローチは次のとおりです。
>
> * カスタム <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> クラスを追加して、アクセス トークンを送信要求にアタッチします。
> * セキュリティで保護された外部 Web API エンドポイントに Web API 要求を送信するために、名前付きの <xref:System.Net.Http.HttpClient> を追加します。
> * 名前付きの <xref:System.Net.Http.HttpClient> を使用して、承認された要求を送信します。
>
> このアプローチの一般的な対象範囲については、記事 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> を参照してください。

ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリの `Program.Main` (`Program.cs`) に、ファクトリを登録します。 アプリの追加のスコープとして `Directory.Read.All` アクセス許可スコープに同意します。

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

`Program.Main` で、グループごとまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。 次の例では、AAD の組み込み "*課金管理者*" ロールに対するポリシーを作成します。

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD ロール オブジェクト ID の完全な一覧については、「[AAD の管理者ロールのグループ ID](#aad-adminstrative-role-group-ids)」セクションを参照してください。

次の例のアプリでは、前述のポリシーを使用してユーザーを承認します。

[`AuthorizeView` コンポーネント](xref:blazor/security/index#authorizeview-component)ではポリシーが使用されます。

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

コンポーネント全体へのアクセスは、[`[Authorize]` 属性ディレクティブ](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) を使用するポリシーを基にして行うことができます。

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

ログインしていないユーザーは、AAD のサインイン ページにリダイレクトされ、サインイン後にコンポーネントに戻されます。

ポリシー チェックは、[手続き型ロジックを使用してコードで実行する](xref:blazor/security/index#procedural-logic)こともできます。

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

## <a name="user-defined-roles"></a>ユーザー定義ロール

ユーザー定義ロールを使用するように、AAD 登録済みアプリを構成することもできます。

`roles` メンバーシップ要求を提供するように Azure portal でアプリを構成するには、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)」を Azure ドキュメントで参照してください。

以下の例では、次の 2 つのロールがアプリに構成されているものとします。

* `admin`
* `developer`

> [!NOTE]
> Azure AD Premium アカウントがないとセキュリティ グループにロールを割り当てることはできませんが、ユーザーをロールに割り当てて、Standard Azure アカウントを使用するユーザーに対する `roles` 要求を受け取ることができます。 このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。
>
> Azure portal で各追加ロール割り当てに対して**_ユーザーを再追加する_** ことにより、複数のロールを割り当てます。

AAD によって送信される単一の `roles` 要求では、ユーザー定義のロールは JSON 配列内の `appRoles` の `value` として示されます。 アプリでは、ロールの JSON 配列を個々の `role` 要求に変換する必要があります。

「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されている `CustomUserFactory` は、JSON 配列値を持つ `roles` 要求に対して動作するように設定されます。 「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示されているように、ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリで `CustomUserFactory` を追加して登録します。 元の `roles` 要求はフレームワークによって自動的に削除されるため、それを削除するためのコードを提供する必要はありません。

ホストされている Blazor ソリューションのクライアント アプリまたはスタンドアロン アプリの `Program.Main` で、ロール要求として "`role`" という名前の要求を指定します。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

この時点でコンポーネントの承認方法が機能しています。 コンポーネント内のすべての承認メカニズムで、`admin` ロールを使用してユーザーを承認できます。

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

## <a name="aad-adminstrative-role-group-ids"></a>AAD の管理者ロールのグループ ID

次の表で示されているオブジェクト ID は、`group` 要求に対する[ポリシー](xref:security/authorization/policies)を作成するために使用されます。 ポリシーでは、アプリ内のさまざまなアクティビティについてユーザーを承認することをアプリに許可します。 詳細については、「[ユーザー定義グループと組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションを参照してください。

AAD 管理者ロール | Object ID
--- | ---
アプリケーション管理者 | fa11557b-4f15-4ddd-85d5-313c7cd74047
アプリケーション開発者 | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
認証管理者 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 管理者 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection 管理者 | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF キーセット管理者 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF ポリシー管理者 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C ユーザー フロー管理者 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C ユーザー フロー属性管理者 | dd0baca0-a535-48c1-b871-8431abe16452
課金管理者 | 69ff516a-b57d-4697-a429-9de4af7b5609
クラウド アプリケーション管理者 | 250b5fe3-b553-458d-9a53-b782c13c34bf
クラウド デバイス管理者 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
コンプライアンス管理者 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
コンプライアンス データ管理者 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
条件付きアクセス管理者 | 8f71a611-137d-49af-87ad-e97f1fd5da76
カスタマー ロックボックスのアクセス承認者 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
デスクトップ Analytics 管理者 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
ディレクトリ閲覧者 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 管理者 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 管理者 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
外部 IdentityID プロバイダー管理者 | febfaeb4-e478-407a-b4b3-f4d9716618a2
全体管理者 | a45ba61b-44db-462c-924b-3b2719152588
グローバル閲覧者 | f6903b21-6aba-4124-b44c-76671796b9d5
グループ管理者 | 158b3e5a-d89d-460b-92b5-3b34985f0197
ゲスト招待元 | 4c730a1d-cc22-44af-8f9f-4eec635c7502
ヘルプデスク管理者 | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune 管理者 | 79950741-23fa-4189-b2cb-46640601c497
Kaizala 管理者 | d6322af2-48e7-42e0-8c68-0bbe31af3412
ライセンス管理者 | 3355458a-e423-44bf-8b98-4ac5e572cea5
メッセージ センターのプライバシー閲覧者 | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
メッセージ センター閲覧者 | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office アプリ管理者 | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
パスワード管理者 | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI 管理者 | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform 管理者 | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
特権認証管理者 | 0829f731-b46d-419f-9742-aeb122367d11
特権ロール管理者 | f20a725a-d1c8-4107-83ea-1171c97d00c7
レポート閲覧者 | 54635450-e8ed-4f2d-9632-07db2517b4de
Search 管理者 | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Search エディター | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
セキュリティ管理者 | 20fa50e3-6531-44d8-bd39-b251420568ad
セキュリティ オペレーター | 43aae017-8e51-4188-91ab-e6debd572800
セキュリティ閲覧者 | 45035cd3-fd97-4250-8197-3a53d3562d9b
サービス サポート管理者 | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint 管理者 | e1c32229-875e-461d-ae24-3cb99116e86c
Skype for Business 管理者 | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams 通信管理者 | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams 通信サポート エンジニア | 802dd94e-d717-46f6-af98-b9167071e9fc
Teams 通信スペシャリスト | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams サービス管理者 | 8846a0be-197b-443a-b13c-11192691fa24
ユーザー管理者 | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
