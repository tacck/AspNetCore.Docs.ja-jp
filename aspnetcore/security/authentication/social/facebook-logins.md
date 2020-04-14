---
title: ASP.NETコアでのFacebook外部ログイン設定
author: rick-anderson
description: 既存のASP.NETコアアプリへのFacebookアカウントのユーザー認証の統合を示すコード例を含むチュートリアル。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277302"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NETコアでのFacebook外部ログイン設定

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

このチュートリアルのコード例では、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Facebook アカウントでサインインできるようにする方法を示します。 まず、[公式の手順](https://developers.facebook.com)に従ってFacebookアプリIDを作成します。

## <a name="create-the-app-in-facebook"></a>フェイスブックでアプリを作成する

* プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)追加します。

* [Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。 Facebookアカウントをお持ちの場合は、ログインページの**Facebookにサインアップ**リンクを使用してアカウントを作成します。  Facebookアカウントをお持ちの場合は、指示に従ってFacebookデベロッパーとして登録します。

* [**マイ アプリ]** メニューから [**アプリの作成**] を選択して新しいアプリ ID を作成します。

   ![開発者向けフェイスブック ポータルマイクロソフト エッジで開く](index/_static/FBMyApps.png)

* フォームに入力し、[アプリ**ID**の作成] ボタンをタップします。

  ![新しいアプリ ID フォームの作成](index/_static/FBNewAppId.png)

* 新しいアプリ カードで、[**製品の追加**] を選択します。  **Facebookログイン**カードで、[セットアップ] をクリックします **。** 

  ![製品のセットアップ ページ](index/_static/FBProductSetup.png)

* **クイックスタート**ウィザードが起動し、最初のページとして**プラットフォームの選択**が行われます。 左下のメニューにある**FaceBook ログイン****設定**リンクをクリックして、今のところウィザードをバイパスします。

  ![クイックスタートをスキップ](index/_static/FBSkipQuickStart.png)

* **[クライアント OAuth 設定]** ページが表示されます。

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* 有効**な OAuth リダイレクト URI**フィールドに */signin-facebook*を追加して開発 URI`https://localhost:44320/signin-facebook`を入力します (例: )。 このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook*ルートで要求を自動的に処理します。

> [!NOTE]
> URI */signin-facebook*は、Facebook 認証プロバイダのデフォルトコールバックとして設定されます。 既定のコールバック URI は、継承された[リモート認証オプションクラスのコールバックパス](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して[、Facebook](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)認証ミドルウェアを構成するときに変更できます。

* [**Save Changes**] をクリックします。

* 左側のナビゲーションで **[基本設定** > ]**リンクを**クリックします。

  このページで、あなたとあなたの`App ID``App Secret`. 次のセクションで、ASP.NET Core アプリケーションに両方を追加します。

* サイトを展開する際には **、Facebookログイン**設定ページに再アクセスし、新しいパブリックURIを登録する必要があります。

## <a name="store-the-facebook-app-id-and-secret"></a>FacebookアプリのIDとシークレットを保存する

[秘密のマネージャー](xref:security/app-secrets)で、FacebookアプリのIDや秘密の値などの機密性の高い設定を保存します。 このサンプルでは、次の手順を実行します。

1. 「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。
1. 秘密鍵とローカルシークレットストアに機密設定を保存`Authentication:Facebook:AppId`し、 `Authentication:Facebook:AppSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>フェイスブック認証の設定

`ConfigureServices` *Startup.cs*ファイルのメソッドにFacebookサービスを追加します。

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>フェイスブックでサインインする

* アプリを実行し、[**ログイン**] を選択します。 
* [**別のサービスを使用してログインする]** で、[Facebook] を選択します。
* 認証のために**Facebook**にリダイレクトされます。
* Facebookの認証情報を入力します。
* メールを設定できるサイトにリダイレクトされます。

これで、Facebookの認証情報を使用してログインしました。

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>外部サインインの承認をキャンセルする反応

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>は、ユーザーが要求された承認要求を承認しない場合に、ユーザー エージェントへのリダイレクト パスを指定できます。

次のコードは、 `AccessDeniedPath` `"/AccessDeniedPathInfo"`をに設定します。

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

このページには`AccessDeniedPath`、次の情報が含まれていることをお勧めします。

*  リモート認証が取り消されました。
* このアプリは認証が必要です。
* サインインを再試行するには、[ログイン] リンクを選択します。

### <a name="test-accessdeniedpath"></a>アクセス拒否パスのテスト

* [facebook.com](https://www.facebook.com/)に移動
* サイン インしている場合は、サインアウトする必要があります。
* アプリを実行し、Facebookサインインを選択します。
* [**今すぐ使用しない**] を選択します。 指定した`AccessDeniedPath`ページにリダイレクトされます。

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 認証でサポートされる設定オプションの詳細については[、FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。 構成オプションは、次の場合に使用できます。

* ユーザーに関するさまざまな情報を要求します。
* クエリ文字列引数を追加して、ログイン エクスペリエンスをカスタマイズします。

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NETコア 2.x のみ:** で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。 このチュートリアルで使用するプロジェクト テンプレートにより、この作業が確実に行われます。
* 初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。 [**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、Facebookで認証する方法を説明しました。 同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。

* Web サイトを Azure Web アプリに公開したら、Facebook`AppSecret`開発者ポータル で をリセットする必要があります。

* と`Authentication:Facebook:AppId`として`Authentication:Facebook:AppSecret`アプリケーションの設定を Azure ポータルで設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
