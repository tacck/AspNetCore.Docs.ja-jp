---
title: ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する
author: guardrex
description: ''
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 2c1454d4fc3cd5923100e27748013873c6b4a74a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402378"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>ASP.NET Core Blazor WebAssembly でホストされるアプリを Azure Active Directory でセキュリティ保護する

作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)

この記事では、認証用に [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) を使用する [Blazor WebAssembly でホストされたアプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。

## <a name="register-apps-in-aad-and-create-solution"></a>AAD でアプリを登録してソリューションを作成する

### <a name="create-a-tenant"></a>テナントの作成

「[クイック スタート: テナントを設定する](/azure/active-directory/develop/quickstart-create-new-tenant)」のガイダンスに従って、AAD でテナントを作成します。

### <a name="register-a-server-api-app"></a>サーバー API アプリを登録する

「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、"*サーバー API アプリ*" 用の AAD アプリを登録した後、次のようにします。

1. **[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。
1. アプリの **[名前]** を指定します (例: **Blazor Server AAD**)。
1. **[サポートされているアカウントの種類]** を選択します。 このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。
1. "*サーバー API アプリ*" の場合、このシナリオでは **[リダイレクト URI]** は必要ないので、ドロップダウンは **[Web]** に設定されたままにして、リダイレクト URI は入力しません。
1. **[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。
1. **[登録]** を選択します。

次の情報を記録しておきます。

* "*サーバー API アプリ*" のアプリケーション ID (クライアント ID) (例: `11111111-1111-1111-1111-111111111111`)
* ディレクトリ ID (テナント ID) (例: `222222222-2222-2222-2222-222222222222`)
* AAD テナント ドメイン (例: `contoso.onmicrosoft.com`): ドメインは、Azure portal で登録されているアプリに対する **[ブランド]** ブレードの **[パブリッシャー ドメイン]** として使用できます。

アプリにはサインインまたはユーザー プロファイルのアクセスは必要ないため、 **[API のアクセス許可]** で、**Microsoft Graph** > **User.Read** アクセス許可を削除します。

**[API の公開]** で:

1. **[Scope の追加]** を選択します。
1. **[Save and continue]\(保存して続行\)** を選択します。
1. **[スコープ名]** を指定します (例: `API.Access`)。
1. **[管理者の同意の表示名]** を指定します (例: `Access API`)。
1. **[管理者の同意の説明]** を指定します (例: `Allows the app to access server app API endpoints.`)。
1. **[状態]** が **[有効]** に設定されていることを確認します。
1. **[スコープの追加]** を選択します。

次の情報を記録しておきます。

* アプリ ID の URI (例: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`、`api://11111111-1111-1111-1111-111111111111`、または指定したカスタム値)
* 既定のスコープ (例: `API.Access`)

### <a name="register-a-client-app"></a>クライアント アプリを登録する

「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app)」のガイダンスおよび以降の Azure AAD のトピックに従って、"*クライアント アプリ*" 用の AAD アプリを登録した後、次のようにします。

1. **[Azure Active Directory]**  >  **[アプリの登録]** で、 **[新規登録]** を選択します。
1. アプリの **[名前]** を指定します (例: **Blazor クライアント AAD**)。
1. **[サポートされているアカウントの種類]** を選択します。 このエクスペリエンスでは、 **[この組織のディレクトリ内のアカウントのみ]** (シングル テナント) を選択できます。
1. **[リダイレクト URI]** ドロップ ダウンの設定を **[Web]** のままとし、次のリダイレクト URI を指定します: `https://localhost:{PORT}/authentication/login-callback`。 Kestrel で実行されているアプリの既定のポートは 5001 です。 アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。 IIS Express の場合、アプリのランダムに生成されたポートは、 **[デバッグ]** パネルのサーバー アプリのプロパティで確認できます。 この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。 「[アプリを作成する](#create-the-app)」セクションの解説には、IIS Express ユーザーに対してリダイレクト URI の更新を促す注意が示されています。
1. **[アクセス許可]**  >  **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** チェックボックスをオフにします。
1. **[登録]** を選択します。

"*クライアント アプリ*" のアプリケーション ID (クライアント ID) を記録しておきます (例: `33333333-3333-3333-3333-333333333333`)。

**[認証]**  >  **[プラットフォーム構成]**  >  **[Web]** で、次を行います。

1. **[リダイレクト URI]** が`https://localhost:{PORT}/authentication/login-callback` であることを確認します。
1. **[暗黙の付与]** で、 **[アクセス トークン]** と **[ID トークン]** のチェック ボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで使用可能です。
1. **[保存]** ボタンを選択します。

**[API のアクセス許可]** で:

1. アプリに **Microsoft Graph** > **User.Read** アクセス許可があることを確認します。
1. **[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。
1. **[名前]** 列で "*サーバー API アプリ*" を選択します (例: **Blazor Server AAD**)。
1. **[API]** の一覧を開きます。
1. API へのアクセスを有効にします (例: `API.Access`)。
1. **[アクセス許可の追加]** を選択します.
1. **[<テナント名> に管理者の同意を与えます]** ボタンを選択します。 **[はい]** を選択して確定します。

### <a name="create-the-app"></a>アプリを作成する

次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンド シェルでこのコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

出力場所を指定するには、パスが含まれるコマンド (`-o BlazorSample` など) に出力オプションを含めます。出力場所としてプロジェクト フォルダーが存在しない場合は、作成されます。 また、フォルダー名は、プロジェクトの名前の一部となります。

> [!NOTE]
> `app-id-uri` オプションにはアプリ ID URI を渡します。ただし、クライアント アプリで構成の変更が必要になる場合があることに注意してください。これについては、「[アクセス トークンのスコープ](#access-token-scopes)」セクションを参照してください。

> [!NOTE]
> Azure portal では、"*クライアント アプリ*" の **[認証]**  >  **[プラットフォーム構成]**  >  **[Web]**  >  **[リダイレクト URI]** は、既定の設定の Kestrel サーバーで実行されるアプリの場合、ポート 5001 に構成されます。
>
> "*クライアント アプリ*" がランダムな IIS Express ポートで実行されている場合、アプリのポートは **[デバッグ]** パネルの "*サーバー アプリ*" のプロパティで確認できます。
>
> 前にポートを "*クライアント アプリ*" の既知のポートで構成しなかった場合は、Azure portal で "*クライアント アプリ*" の登録に戻り、正しいポートでリダイレクト URI を更新します。

## <a name="server-app-configuration"></a>サーバー アプリの構成

"*このセクションは、ソリューションの **`Server`** アプリに関連しています。* "

### <a name="authentication-package"></a>認証パッケージ

ASP.NET Core Web API の呼び出しの認証と承認のサポートは、[`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) パッケージによって提供されます。

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>認証サービスのサポート

`AddAuthentication` メソッドにより、アプリ内での認証サービスが設定され、JWT ベアラー ハンドラーが既定の認証方法として構成されます。 <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> メソッドにより、Azure Active Directory によって送信されるトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターが設定されます。

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> により、次のようになります。

* アプリにより、受信要求のトークンの解析と検証が試みられます。
* 適切な資格情報なしで保護されたリソースへのアクセスを試みた要求は失敗します。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

既定では、サーバー アプリ API により、`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類からの値が `User.Identity.Name` に設定されます (例: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`)。

`name` 要求の種類から値を受け取るようにアプリを構成するには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> の <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> を構成します。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>アプリの設定

`appsettings.json` ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

例:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast コントローラー

WeatherForecast コントローラー (*Controllers/WeatherForecastController.cs*) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性がコントローラーに適用されている保護された API が公開されます。 次のことを理解しておくことが**重要**です。

* この API コントローラーの [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、この API を不正アクセスから保護する唯一のものです。
* Blazor WebAssembly アプリで使用される [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性は、アプリが正しく動作するにはユーザーを承認する必要がある、というアプリへのヒントとしてのみ機能します。

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>クライアント アプリの構成

"*このセクションは、ソリューションの **`Client`** アプリに関連しています。* "

### <a name="authentication-package"></a>認証パッケージ

職場または学校アカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリでユーザーを認証し、保護された API を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって、[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージがアプリに推移的に追加されます。

### <a name="authentication-service-support"></a>認証サービスのサポート

サーバー プロジェクトへの要求を行うときのアクセス トークンが含まれる <xref:System.Net.Http.HttpClient> インスタンスのサポートが追加されます。

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample.ServerAPI`)。

ユーザーの認証に対するサポートは、[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) パッケージによって提供される <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 拡張メソッドを使用して、サービス コンテナーに登録されます。 このメソッドでは、アプリが IdentityID プロバイダー (IP) とやり取りするために必要なサービスが設定されます。

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> メソッドでは、アプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。 アプリを構成するために必要な値は、アプリを登録するときに Azure Portal の AAD の構成から取得できます。

構成は `wwwroot/appsettings.json` ファイルによって提供されます。

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

例:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>アクセス トークン スコープ

既定のアクセス トークン スコープでは、次のようなアクセス トークン スコープの一覧が表されます。

* サインイン要求に既定で含まれます。
* 認証直後にアクセス トークンをプロビジョニングするために使用されます。

すべてのスコープは、Azure Active Directory の規則に従って同じアプリに属している必要があります。 必要に応じて追加の API アプリ用に追加のスコープを追加できます。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

詳細については、"*その他のシナリオ*" に関する記事の次のセクションを参照してください。

* [追加のアクセス トークンを要求する](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [送信要求にトークンを添付する](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>インポート ファイル

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>インデックス ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay コンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData コンポーネント

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>アプリを実行する

サーバー プロジェクトからアプリを実行します。 Visual Studio を使用しているときは、次のいずれかを行います。

* ツール バーの **[スタートアップ プロジェクト]** ドロップダウン リストを "*サーバー API アプリ*" に設定して、 **[実行]** ボタンを選択します。
* **ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを開始します。

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/security/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID プラットフォームのドキュメント](/azure/active-directory/develop/)
