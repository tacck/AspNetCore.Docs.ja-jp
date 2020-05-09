---
title: Azure Active Directory を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: aaae2b755d6d6e74db0cb7676820d01964c2add4
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976806"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure Active Directory を使用Blazorして ASP.NET Core webasのホスト型アプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [ Blazorアプリケーション](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C でのアプリの登録とソリューションの作成

### <a name="create-a-tenant"></a>テナントの作成

[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。

### <a name="register-a-server-api-app"></a>サーバー API アプリを登録する

「[クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。

1. **[新規登録]** を選択します。
1. アプリの**名前**を指定します (たとえば、 ** Blazor Server AAD**)。
1. **サポートされているアカウントの種類**を選択します。 このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。
1. このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。
1. [ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。
1. **[登録]** を選択します。

[ **API のアクセス許可**] で、 **Microsoft Graph** > **ユーザーを削除します。** アプリはサインインまたはプロファイルへのアクセスを必要としないため、読み取りアクセス許可を削除します。

で**API を公開**します。

1. **[Scope の追加]** を選択します。
1. **[Save and continue]** (保存して続行) を選択します。
1. **スコープ名**(など`API.Access`) を指定します。
1. **管理者の同意の表示名**を指定し`Access API`ます (たとえば、)。
1. **管理者の同意の説明**を入力し`Allows the app to access server app API endpoints.`ます (例:)。
1. **状態**が**有効**に設定されていることを確認します。
1. **[スコープの追加]** を選択します。

次の情報を記録します。

* *サーバー API アプリ*アプリケーション ID (クライアント ID) (など`11111111-1111-1111-1111-111111111111`)
* アプリ ID URI (たとえば`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`、、、または指定したカスタム値)
* ディレクトリ ID (テナント ID) (など`222222222-2222-2222-2222-222222222222`)
* AAD テナントドメイン (例`contoso.onmicrosoft.com`)
* 既定のスコープ (例、 `API.Access`)

### <a name="register-a-client-app"></a>クライアント アプリを登録する

[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。

1. **[新規登録]** を選択します。
1. アプリの**名前**( ** Blazorクライアント AAD**など) を指定します。
1. **サポートされているアカウントの種類**を選択します。 このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。
1. [**リダイレクト uri** ] ドロップダウンを [ **Web**] に設定し、の`https://localhost:5001/authentication/login-callback`リダイレクト uri を指定します。
1. [ **Permissions** > **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。
1. **[登録]** を選択します。

[**認証** > **プラットフォーム構成** > **Web**:

1. の`https://localhost:5001/authentication/login-callback` **リダイレクト URI**が存在することを確認します。
1. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで許容されます。
1. **[保存]** を選択します。

**API のアクセス許可**:

1. アプリに**Microsoft Graph** > ユーザーがあることを確認**します。読み取り**アクセス許可。
1. [**アクセス許可の追加]、** **[api**の追加] の順に選択します。
1. [**名前**] 列から*サーバー API アプリ*を選択します ( ** Blazorサーバー AAD**など)。
1. **API**の一覧を開きます。
1. API へのアクセスを有効にします`API.Access`(たとえば、)。
1. **[アクセス許可の追加]** を選択します.
1. [ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。 **[はい]** をクリックして操作を確定します。

*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば`33333333-3333-3333-3333-333333333333`、)。

### <a name="create-the-app"></a>アプリを作成する

次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など`-o BlazorSample`) を指定して出力オプションを含めます。 フォルダー名もプロジェクトの名前の一部になります。

> [!NOTE]
> アプリ ID URI を`app-id-uri`オプションに渡しますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。

## <a name="server-app-configuration"></a>サーバーアプリの構成

*このセクションは、ソリューションの**サーバー**アプリに関連しています。*

### <a name="authentication-package"></a>認証パッケージ

ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、 `Microsoft.AspNetCore.Authentication.AzureAD.UI`次のとおりです。

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>認証サービスのサポート

メソッド`AddAuthentication`は、アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。 メソッド`AddAzureADBearer`は、Azure Active Directory によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`次`UseAuthorization`のことを確認します。

* アプリは、受信要求のトークンの解析と検証を試みます。
* 適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>ユーザーズ.Identity.指定

既定では、サーバーアプリ API は`User.Identity.Name` 、 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`要求の種類 (など`2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) の値を設定します。

要求の種類から値を受け取るようにアプリを構成するには<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 、で`Startup.ConfigureServices`の[Tokenvalidationparameters. nameclaimtype を構成します。](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) `name`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>アプリの設定

*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。

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

WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が`[Authorize]`適用された保護された API を公開します。 次の**点**を理解しておくことが重要です。

* この`[Authorize]` api コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。
* Webassembly で使用される属性は`[Authorize]` 、アプリへのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。 Blazor

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

## <a name="client-app-configuration"></a>クライアントアプリの構成

*このセクションは、ソリューションの**クライアント**アプリに関連しています。*

### <a name="authentication-package"></a>認証パッケージ

職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

前`{VERSION}`のパッケージ参照のを、 <xref:blazor/get-started>この記事に示さ`Microsoft.AspNetCore.Blazor.Templates`れているパッケージのバージョンに置き換えます。

`Microsoft.Authentication.WebAssembly.Msal`パッケージによって、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`パッケージが推移的にアプリに追加されます。

### <a name="authentication-service-support"></a>認証サービスのサポート

サーバープロジェクト`HttpClient`への要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

ユーザー認証のサポートは、 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`パッケージによって提供される拡張メソッドを使用して、サービスコンテナーに登録されます。 このメソッドは、アプリがIdentityプロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

メソッド`AddMsalAuthentication`は、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。

構成は*wwwroot/appsettings*ファイルによって提供されます。

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

### <a name="access-token-scopes"></a>アクセストークンスコープ

既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。

* サインイン要求に既定で含まれています。
* 認証直後にアクセストークンをプロビジョニングするために使用されます。

すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。 必要に応じて追加の API アプリ用に追加のスコープを追加できます。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure portal がスコープ URI を提供し、**アプリ**が API から401の承認されて*い*ない応答を受信したときにハンドルされない例外をスローした場合は、スキームとホストを含まないスコープ uri を使用してみてください。 たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> スキームとホストなしでスコープ URI を指定します。
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

詳細については、*追加のシナリオ*に関する記事の次のセクションを参照してください。

* [追加のアクセス トークンを要求する](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [送信要求にトークンを添付する](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>ファイルのインポート

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>アプリコンポーネント

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

サーバープロジェクトからアプリを実行します。 Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID プラットフォームのドキュメント](/azure/active-directory/develop/)
