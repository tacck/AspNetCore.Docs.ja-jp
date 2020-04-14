---
title: ASP.NETコアを使用したTwitter外部サインイン設定
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277289"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NETコアを使用したTwitter外部サインイン設定

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[自分の Twitter アカウントでサインイン](https://dev.twitter.com/web/sign-in/desktop-browser)できるようにする方法を示します。

## <a name="create-the-app-in-twitter"></a>ツイッターでアプリを作成する

* プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0)追加します。

* に[https://apps.twitter.com/](https://apps.twitter.com/)移動し、サインインします。 Twitter アカウントをまだお持ちの場合は、[**[今すぐサインアップ](https://twitter.com/signup)**] リンクを使用してアカウントを作成します。

* [**アプリの作成] を**選択します。 **アプリ名**、**アプリケーションの説明**、およびパブリック**Web サイト**URI を入力します (ドメイン名を登録するまでは一時的に行うことができます)。

* [Twitterでサインインを**有効にする] の横にあるチェックボックスをオンにします。**

* 既定では、ユーザーに電子メール アドレスを設定する必要があります。 [**アクセス権**] タブに移動し、[**編集**] ボタンをクリックして、[**ユーザーからの電子メール アドレスの要求]** の横にあるチェックボックスをオンにします。

* **[コールバック URL]** フィールドに`/signin-twitter`追加された開発 URI を入力します`https://webapp128.azurewebsites.net/signin-twitter`(例: )。 このサンプルで後述する Twitter 認証スキームは、OAuth フローを実装するために、ルートで`/signin-twitter`要求を自動的に処理します。

  > [!NOTE]
  > URI セグメント`/signin-twitter`は、Twitter 認証プロバイダーのデフォルトのコールバックとして設定されます。 既定のコールバック URI は、継承された[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)クラスのプロパティを使用して、Twitter 認証ミドル[ウェアを構成](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)するときに変更できます。

* フォームの残りの部分に入力し、[**作成**] を選択します。 新しいアプリケーションの詳細が表示されます。

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Twitter コンシューマー API キーとシークレットを保存する

秘密マネージャーで、Twitter コンシューマー API キーやシークレットなどの機密性の高い設定を保存[します](xref:security/app-secrets)。 このサンプルでは、次の手順を実行します。

1. 「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。
1. 秘密キーとローカルシークレットストアに機密設定を保存`Authentication:Twitter:ConsumerKey`し、 `Authentication:Twitter:ConsumerSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

これらのトークンは、新しいTwitterアプリケーションを作成した後に **[キーとアクセストークン**]タブで見つけることができます。

## <a name="configure-twitter-authentication"></a>ツイッター認証の設定

ファイル内の`ConfigureServices`メソッドにTwitterサービス*Startup.cs*追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter 認証でサポートされる設定オプションの詳細については[、TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求するために使用できます。

## <a name="sign-in-with-twitter"></a>ツイッターでサインインする

アプリを実行し、[**ログイン**] を選択します。 Twitterでログインするオプションが表示されます。

**Twitter**をクリックすると、認証のためにTwitterにリダイレクトされます。

Twitterの認証情報を入力すると、メールを設定できる Web サイトにリダイレクトされます。

これで、Twitter の資格情報を使用してログインします。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NETコア 2.x のみ:** で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。 このサンプルで使用されているプロジェクト テンプレートによって、この処理が確実に行われます。
* 初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。 [**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、Twitterで認証する方法を説明しました。 同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。

* Web サイトを Azure Web アプリに発行したら、Twitter`ConsumerSecret`開発者ポータル で をリセットする必要があります。

* と`Authentication:Twitter:ConsumerKey`として`Authentication:Twitter:ConsumerSecret`アプリケーションの設定を Azure ポータルで設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
