---
title: BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9e76b300c159a2a1432aa4b1c6e47b3d91084a85
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84215098"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>BlazorAzure Active Directory を使用して ASP.NET Core webasのホスト型アプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [ Blazor アプリケーション](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。

## <a name="register-apps-in-aad-and-create-solution"></a>AAD にアプリを登録してソリューションを作成する

### <a name="create-a-tenant"></a>テナントの作成

[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。

### <a name="register-a-server-api-app"></a>サーバー API アプリを登録する

[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、*サーバー API アプリ*に AAD アプリを登録し、次の手順を実行します。

1. **Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。
1. アプリの**名前**を指定します (たとえば、 ** Blazor Server AAD**)。
1. **サポートされているアカウントの種類**を選択します。 このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。
1. このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは [ **Web** ] に設定し、リダイレクト uri は入力しないでください。
1. [ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。
1. **[登録]** を選択します。

次の情報を記録します。

* *サーバー API アプリ*アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )
* ディレクトリ ID (テナント ID) (など `222222222-2222-2222-2222-222222222222` )
* AAD テナントドメイン (例 `contoso.onmicrosoft.com` :): 登録されているアプリの Azure portal の [**ブランド**化] ブレードで、**発行元ドメイン**としてドメインを使用できます。

[ **API のアクセス許可**] で、 **Microsoft Graph**ユーザーを削除  >  **します。** アプリはサインインまたはユーザープロファイルのアクセスを必要としないため、読み取りアクセス許可を削除します。

で**API を公開**します。

1. **[Scope の追加]** を選択します。
1. **[Save and continue]** (保存して続行) を選択します。
1. **スコープ名**(など) を指定 `API.Access` します。
1. **管理者の同意の表示名**を指定します (たとえば、 `Access API` )。
1. **管理者の同意の説明**を入力します (例: `Allows the app to access server app API endpoints.` )。
1. **状態**が**有効**に設定されていることを確認します。
1. **[スコープの追加]** を選択します。

次の情報を記録します。

* アプリ ID URI (たとえば、、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` 、 `api://11111111-1111-1111-1111-111111111111` または指定したカスタム値)
* 既定のスコープ (例、 `API.Access` )

### <a name="register-a-client-app"></a>クライアント アプリを登録する

[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、*クライアントアプリ*に AAD アプリを登録し、次の手順を実行します。

1. **Azure Active Directory**  >  **アプリの登録**で、[**新規登録**] を選択します。
1. アプリの**名前**( ** Blazor クライアント AAD**など) を指定します。
1. **サポートされているアカウントの種類**を選択します。 このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。
1. [**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。 Kestrel で実行されているアプリの既定のポートは5001です。 アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。 IIS Express の場合、アプリのランダムに生成されたポートは、サーバーアプリの [**デバッグ**] パネルの [プロパティ] にあります。 この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。 「[アプリを作成](#create-the-app)する」セクションにコメントが表示され、ユーザーにリダイレクト URI の更新を IIS Express ように通知します。
1. [ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。
1. **[登録]** を選択します。

*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、 `33333333-3333-3333-3333-333333333333` )。

[**認証**  >  **プラットフォーム構成**  >  **Web**:

1. の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。
1. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで許容されます。
1. **[保存]** を選択します。

**API のアクセス許可**:

1. アプリに**Microsoft Graph**ユーザーがあることを確認  >  **します。読み取り**アクセス許可。
1. [**アクセス許可の追加]、** **[api**の追加] の順に選択します。
1. [**名前**] 列から*サーバー API アプリ*を選択します ( ** Blazor サーバー AAD**など)。
1. **API**の一覧を開きます。
1. API へのアクセスを有効にします (たとえば、 `API.Access` )。
1. **[アクセス許可の追加]** を選択します。
1. [ **{テナント名} の管理者コンテンツを付与**] ボタンを選択します。 **[はい]** をクリックして操作を確定します。

### <a name="create-the-app"></a>アプリを作成する

次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。 フォルダー名もプロジェクトの名前の一部になります。

> [!NOTE]
> アプリ ID URI をオプションに渡し `app-id-uri` ますが、クライアントアプリで構成の変更が必要になる場合があることに注意してください。詳細については、「[アクセストークンのスコープ](#access-token-scopes)」セクションを参照してください。

> [!NOTE]
> Azure portal では、*クライアントアプリの***認証**  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。
>
> *クライアントアプリ*がランダム IIS Express ポートで実行されている場合、アプリケーションのポートは、[**デバッグ**] パネルの*サーバーアプリの*[プロパティ] にあります。
>
> ポートが*クライアントアプリの*既知のポートで事前に構成されていない場合は、Azure portal で*クライアントアプリの*登録に戻り、正しいポートでリダイレクト URI を更新します。

## <a name="server-app-configuration"></a>サーバーアプリの構成

*このセクションは、ソリューションの**サーバー**アプリに関連しています。*

### <a name="authentication-package"></a>認証パッケージ

ASP.NET Core Web Api の呼び出しの認証と承認のサポートは、 [AzureAD](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/)パッケージによって提供されます。

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>認証サービスのサポート

メソッドは、 `AddAuthentication` アプリ内に認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。 メソッドは、 <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> Azure Active Directory によって出力されたトークンを検証するために必要な JWT ベアラーハンドラーの特定のパラメーターを設定します。

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>次のことを確認します。

* アプリは、受信要求のトークンの解析と検証を試みます。
* 適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>ユーザー. Identity .指定

既定では、サーバーアプリ API は、 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 要求の種類 (など) の値を設定し `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ます。

要求の種類から値を受け取るようにアプリを構成するには、 `name` での[Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を構成します。 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`

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

WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに属性が適用された保護された API を公開します [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。 次の**点**を理解しておくことが重要です。

* この api [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) コントローラーの属性は、この api を不正アクセスから保護する唯一の方法です。
* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Webassembly で使用される属性は、アプリ Blazor へのヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。

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

職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)([Microsoft. authentication. Webas. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

この[Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)パッケージは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージを推移的にアプリに追加します。このパッケージは、アプリケーションに対して推移的に追加されます。

### <a name="authentication-service-support"></a>認証サービスのサポート

<xref:System.Net.Http.HttpClient>サーバープロジェクトへの要求を行うときに、アクセストークンを含むインスタンスのサポートが追加されます。

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

ユーザー認証のサポートは、サービスコンテナーに登録され <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> ている拡張メソッドを[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)使用して、サービスコンテナーに登録されます。 このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。

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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

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

サーバープロジェクトからアプリを実行します。 Visual Studio を使用する場合は、次のいずれかの方法で行います。

* ツールバーの [**スタートアッププロジェクト**] ドロップダウンリストを*サーバー API アプリ*に設定し、[**実行**] ボタンを選択します。
* **ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* <xref:security/blazor/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID プラットフォームのドキュメント](/azure/active-directory/develop/)
