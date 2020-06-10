---
title: BlazorAzure Active Directory を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b85129d8b56f2106636b47534630f8139e100ae9
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851212"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>BlazorAzure Active Directory を使用して ASP.NET Core のスタンドアロンアプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

Blazor認証に[AZURE ACTIVE DIRECTORY (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する webassembly アプリを作成するには、次のようにします。

[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。

Azure portal の**Azure Active Directory**  >  **アプリの登録**領域に AAD アプリを登録します。

1. アプリの**名前**を指定します ( ** Blazor スタンドアロン AAD**など)。
1. **サポートされているアカウントの種類**を選択します。 この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。
1. [**リダイレクト uri** ] ドロップダウンを [ **Web** ] に設定し、[リダイレクト uri] を指定します `https://localhost:{PORT}/authentication/login-callback` 。 Kestrel で実行されているアプリの既定のポートは5001です。 アプリが別の Kestrel ポートで実行されている場合は、アプリのポートを使用します。 IIS Express の場合、アプリのランダムに生成されたポートは、[**デバッグ**] パネルのアプリの [プロパティ] にあります。 この時点ではアプリは存在せず、IIS Express ポートは不明であるため、アプリが作成された後にこの手順に戻り、リダイレクト URI を更新してください。 このトピックの後半では、リダイレクト URI を更新するようにユーザーに IIS Express 通知するために、このトピックの後半でコメントを表示します。
1. [ **Permissions**  >  **求めるプロンプト to openid and offline_access permissions** ] チェックボックスをオフにします。
1. **[登録]** を選択します。

次の情報を記録します。

* アプリケーション ID (クライアント ID) (など `11111111-1111-1111-1111-111111111111` )
* ディレクトリ ID (テナント ID) (など `22222222-2222-2222-2222-222222222222` )

[**認証**  >  **プラットフォーム構成**  >  **Web**:

1. の**リダイレクト URI**が存在することを確認 `https://localhost:{PORT}/authentication/login-callback` します。
1. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで許容されます。
1. **[保存]** を選択します。

アプリを作成します。 次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパス (など) を指定して出力オプションを含め `-o BlazorSample` ます。 フォルダー名もプロジェクトの名前の一部になります。

> [!NOTE]
> Azure portal では、アプリの**認証**  >  **プラットフォーム構成**  >  **Web**  >  **リダイレクト URI**は、kestrel サーバーで既定の設定で実行されるアプリのポート5001用に構成されています。
>
> アプリがランダム IIS Express ポートで実行されている場合、アプリのポートは [**デバッグ**] パネルのアプリの [プロパティ] にあります。
>
> ポートがアプリの既知のポートで事前に構成されていない場合は、Azure portal でアプリの登録に戻り、正しいポートでリダイレクト URI を更新します。

アプリを作成すると、次のことができるようになります。

* AAD ユーザーアカウントを使用してアプリにログインします。
* Microsoft Api のアクセストークンを要求します。 詳細については次を参照してください:
  * [アクセストークンスコープ](#access-token-scopes)
  * [クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する。

## <a name="authentication-package"></a>認証パッケージ

職場または学校のアカウント () を使用するようにアプリを作成すると、 `SingleOrg` アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)([Microsoft. authentication. Webas. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

この[Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)パッケージは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージを推移的にアプリに追加します。このパッケージは、アプリケーションに対して推移的に追加されます。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、サービスコンテナーに登録され <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> ている拡張メソッドを[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)使用して、サービスコンテナーに登録されます。 このメソッドは、アプリがプロバイダー (IP) と対話するために必要なサービスを設定し Identity ます。

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリを構成するために必要な値は、アプリを登録するときに AAD 構成から取得できます。

構成は*wwwroot/appsettings*ファイルによって提供されます。

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

例:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>アクセストークンスコープ

BlazorWebassembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。 サインインフローの一部としてアクセストークンをプロビジョニングするには、の既定のアクセストークンスコープにスコープを追加し <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ます。

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

## <a name="imports-file"></a>ファイルのインポート

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay コンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/blazor/webassembly/additional-scenarios>
* [セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID プラットフォームのドキュメント](/azure/active-directory/develop/)
