---
title: ASP.NET Core での Facebook 外部ログインセットアップ
author: rick-anderson
description: Facebook アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を示すコード例を紹介したチュートリアルです。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777034"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Facebook 外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Facebook アカウントでサインインできるようにする方法を示します。 まず、[正式な手順](https://developers.facebook.com)に従って FACEBOOK アプリ ID を作成します。

## <a name="create-the-app-in-facebook"></a>Facebook でアプリを作成する

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet パッケージをプロジェクトに追加します。

* [Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。 まだ Facebook アカウントを持っていない場合は、ログインページの [ **facebook へのサインアップ**] リンクを使用して作成します。  Facebook アカウントを作成したら、手順に従って Facebook 開発者として登録します。

* **[マイアプリ**] メニューで、[**アプリの作成**] を選択して新しいアプリ ID を作成します。

   ![開発者向け Facebook ポータルを Microsoft Edge で開く](index/_static/FBMyApps.png)

* フォームに入力し、[**アプリ ID の作成**] ボタンをタップします。

  ![新しいアプリ ID フォームを作成する](index/_static/FBNewAppId.png)

* 新しいアプリカードで、[**製品の追加**] を選択します。  **Facebook ログイン**カードで、 **[セットアップ] をクリックし**ます。 

  ![製品のセットアップページ](index/_static/FBProductSetup.png)

* **クイックスタート**ウィザードが起動し、最初のページとして **[プラットフォーム] を選択**します。 ここでウィザードをバイパスするには、左下のメニューで [ **FaceBook のログイン****設定**] リンクをクリックします。

  ![スキップクイックスタート](index/_static/FBSkipQuickStart.png)

* [**クライアントの OAuth 設定**] ページが表示されます。

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* [**有効な OAuth リダイレクト uri** ] フィールドに */signin-facebook*を追加して、開発 URI を`https://localhost:44320/signin-facebook`入力します (例:)。 このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook* route で要求を自動的に処理します。

> [!NOTE]
> URI */signin-facebook*は、facebook 認証プロバイダーの既定のコールバックとして設定されます。 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Facebook 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* **[変更を保存]** をクリックします。

* 左側のナビゲーションで、[**設定** > ] [**基本**リンク] の順にクリックします。

  このページで、 `App ID`とをメモしておき`App Secret`ます。 次のセクションでは、両方を ASP.NET Core アプリケーションに追加します。

* サイトをデプロイするときに、 **Facebook ログイン**のセットアップページを再表示し、新しいパブリック URI を登録する必要があります。

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook アプリ ID とシークレットを保存する

Facebook アプリ ID やシークレット値などの機微な設定を[Secret Manager](xref:security/app-secrets)に保存します。 このサンプルでは、次の手順を使用します。

1. 「[シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。
1. 秘密キーとシークレットキー `Authentication:Facebook:AppId`を使用して、機密設定をローカル`Authentication:Facebook:AppSecret`シークレットストアに保存します。

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook 認証の構成

`ConfigureServices` *Startup.cs*ファイルのメソッドに Facebook サービスを追加します。

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Facebook でサインインする

* アプリを実行し、[**ログイン**] を選択します。 
* [**別のサービスを使用してログインする**] で、[Facebook] を選択します。
* 認証のために**Facebook**にリダイレクトされます。
* Facebook の資格情報を入力します。
* メールを設定できるサイトにリダイレクトされます。

Facebook の資格情報を使用してログインしました。

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>外部サインインの承認の取り消しに対処する

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>ユーザーが要求された承認要求を承認しない場合、ユーザーエージェントへのリダイレクトパスを提供できます。

次のコードでは`AccessDeniedPath` 、 `"/AccessDeniedPathInfo"`をに設定しています。

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

ページには`AccessDeniedPath`次の情報が含まれていることをお勧めします。

*  リモート認証が取り消されました。
* このアプリには認証が必要です。
* もう一度サインインを試すには、[ログイン] リンクを選択します。

### <a name="test-accessdeniedpath"></a>テスト AccessDeniedPath

* [Facebook.com](https://www.facebook.com/)に移動します。
* サインインしている場合は、サインアウトする必要があります。
* アプリを実行し、[Facebook サインイン] を選択します。
* [**今後**] を選択します。 指定さ`AccessDeniedPath`れたページにリダイレクトされます。

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 認証でサポートされる構成オプションの詳細については、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。 構成オプションは、次の場合に使用できます。

* ユーザーに関するさまざまな情報を要求します。
* クエリ文字列引数を追加して、ログインエクスペリエンスをカスタマイズします。

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ:** でIdentity `ConfigureServices`を呼び出す`services.AddIdentity`ことによって構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。
* 初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。 [**移行の適用**] をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、Facebook で認証する方法について説明しました。 同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、Facebook 開発者ポータル`AppSecret`でをリセットする必要があります。

* `Authentication:Facebook:AppId`と`Authentication:Facebook:AppSecret`を、Azure portal のアプリケーション設定として設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
