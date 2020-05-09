---
title: Azure Active Directory Blazorのグループと役割を使用した ASP.NET Core webas
author: guardrex
description: Azure Active Directory グループとロールBlazorを使用するように webassembly 構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: afdb5ddc4d4ed08d0f1ecaf7158af283dda6b302
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976883"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD グループ、管理者ロール、およびユーザー定義のロール

[Luke Latham](https://github.com/guardrex)および[Javier Calvarro jeannine](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Azure Active Directory (AAD) には、ASP.NET Core Id と組み合わせることができるいくつかの承認方法が用意されています。

* ユーザー定義グループ
  * Security
  * O365
  * Distribution
* ロール
  * 組み込みの管理者ロール
  * ユーザー定義ロール

この記事のガイダンスは、次のトピックで説明する Blazor Webasaad 展開シナリオに適用されます。

* [Microsoft アカウントによるスタンドアロン](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [AAD によるスタンドアロン](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [AAD によるホスティング](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>ユーザー定義グループと組み込みの管理者ロール

`groups`メンバーシップ要求を提供するように Azure portal でアプリを構成するには、次の Azure の記事を参照してください。 ユーザー定義の AAD グループと組み込みの管理者ロールにユーザーを割り当てます。

* [Azure AD セキュリティ グループを使用したロール](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [groupMembershipClaims 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

次の例では、AAD の組み込み*課金管理者*ロールにユーザーが割り当てられていることを前提としています。

AAD に`groups`よって送信された単一の要求は、ユーザーのグループとロールを JSON 配列のオブジェクト Id (guid) として提示します。 アプリでは、グループとロールの JSON 配列を、アプリ`group`が[ポリシー](xref:security/authorization/policies)を作成できる個々の要求に変換する必要があります。

を`RemoteUserAccount`拡張して、グループとロールの配列プロパティを含めます。

*CustomUserAccount.cs*:

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

ホスト型ソリューションのスタンドアロンアプリまたはクライアントアプリでカスタムユーザーファクトリを作成します。 次のファクトリは、[ユーザー定義](#user-defined-roles)の`roles`ロールセクションで説明されている要求配列を処理するようにも構成されています。

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

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

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

元`groups`の要求を削除するためのコードを提供する必要はありません。これは、フレームワークによって自動的に削除されるためです。

ホストされて`Program.Main`いるソリューションのスタンドアロンアプリまたはクライアントアプリのファクトリを (*Program.cs*) に登録します。

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

で`Program.Main`、グループまたはロールごとに[ポリシー](xref:security/authorization/policies)を作成します。 次の例では、AAD の組み込み*課金管理者*ロールのポリシーを作成します。

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD ロールオブジェクト Id の完全な一覧については、「 [aad のロールグループ id](#aad-adminstrative-role-group-ids) 」セクションを参照してください。

次の例では、アプリは前述のポリシーを使用してユーザーを承認します。

[Authorizeview コンポーネント](xref:security/blazor/index#authorizeview-component)は、ポリシーと連携します。

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

コンポーネント全体へのアクセスは、 [ `[Authorize]`属性ディレクティブ](xref:security/blazor/index#authorize-attribute)ディレクティブを使用して、ポリシーに基づいて行うことができます。

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

ユーザーがログインしていない場合は、AAD のサインインページにリダイレクトされ、サインイン後にコンポーネントに戻ります。

また、手続き型の[ロジックを使用して、コードで](xref:security/blazor/index#procedural-logic)ポリシーチェックを実行することもできます。

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

### <a name="user-defined-roles"></a>ユーザー定義ロール

AAD で登録されたアプリは、ユーザー定義のロールを使用するように構成することもできます。

`roles`メンバーシップ要求を提供するために Azure portal でアプリを構成する方法については、「[方法: アプリケーションにアプリロールを追加し](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)、Azure ドキュメントのトークンで受信する」を参照してください。

次の例では、アプリが2つのロールで構成されていることを前提としています。

* `admin`
* `developer`

> [!NOTE]
> Azure AD Premium アカウントを使用せずにセキュリティグループにロールを割り当てることはできませんが、ユーザーを`roles`ロールに割り当て、標準の Azure アカウントを持つユーザーの要求を受け取ることができます。 このセクションのガイダンスでは、Azure AD Premium アカウントは必要ありません。
>
> Azure portal に複数のロールが割り当てられている場合は、追加のロールの割り当てごとに**_ユーザーを再度追加_** します。

AAD に`roles`よって送信された1つの要求は、 `appRoles`ユーザー `value`定義のロールを JSON 配列内のとして提示します。 アプリでは、ロールの JSON 配列を個々`role`の要求に変換する必要があります。

「 `CustomUserFactory` [ユーザー定義グループと AAD の組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションに示されているは、JSON 配列値`roles`を持つクレームに対して動作するように設定されています。 「 `CustomUserFactory` [ユーザー定義グループと AAD の組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)」セクションで示したように、ホストされているソリューションのスタンドアロンアプリまたはクライアントアプリにを追加して登録します。 元`roles`の要求を削除するためのコードを提供する必要はありません。これは、フレームワークによって自動的に削除されるためです。

スタンド`Program.Main`アロンアプリまたはホストされたソリューションのクライアントアプリで、ロール要求と`role`して "" という名前の要求を指定します。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

コンポーネントの承認方法は、この時点で機能します。 コンポーネント内のすべての承認メカニズムは、 `admin`ロールを使用してユーザーを承認できます。

* [Authorizeview コンポーネント](xref:security/blazor/index#authorizeview-component)(例: `<AuthorizeView Roles="admin">`)
* attribute ディレクティブ (例: `@attribute [Authorize(Roles = "admin")]`) [ `[Authorize]` ](xref:security/blazor/index#authorize-attribute)
* [手続き型ロジック](xref:security/blazor/index#procedural-logic)(例`if (user.IsInRole("admin")) { ... }`:)

  複数のロールテストがサポートされています。

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>AAD のロールグループ Id

次の表に示すオブジェクト Id は、要求の`group` [ポリシー](xref:security/authorization/policies)を作成するために使用されます。 ポリシーを使用すると、アプリ内のさまざまなアクティビティについてユーザーを承認することができます。 詳細については、[ユーザー定義グループと AAD の組み込み管理者ロール](#user-defined-groups-and-built-in-administrative-roles)に関するセクションを参照してください。

AAD 管理者ロール | Object ID
--- | ---
アプリケーション管理者 | fa11557b-4f15-4ddd-85d5-313c7cd74047
アプリケーション開発者 | 4 ~ 6 8-4f6-89f2-5cd48dc74a2c
認証管理者 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 管理者 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection 管理者 | 18632dcef9b547 f01-abb5-37051f06860e
B2C IEF キーセット管理者 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF ポリシー管理者 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C ユーザーフロー管理者 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C ユーザーフロー属性管理者 | dd0baca0-a535-48c1-b871-8431abe16452
課金管理者 | 69ff516a-b57d-4697-a429-9de4af7b5609
クラウド アプリケーション管理者 | 250b5fe3-b553-458d-9a53-b782c13c34bf
クラウド デバイス管理者 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
コンプライアンス管理者 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
コンプライアンス データ管理者 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
条件付きアクセス管理者 | 8f71a611-137d49af87 e97f1fd5da76
カスタマー ロックボックスのアクセス承認者 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
デスクトップ分析管理者 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
ディレクトリ閲覧者 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 管理者 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 管理者 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
外部Identityプロバイダー管理者 | febfaeb4-e478-407a-b4b3-f4d9716618a2
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
Office アプリ管理者 | 5f3870cdb042-4f93-86d7-c9d77c664dc7
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
チームのコミュニケーションスペシャリスト | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams サービス管理者 | 8846a0be-197b-443a-b13c-11192691fa24
ユーザー管理者 | 1f6eed58-7dd3-460ba298-666f975427a1

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
