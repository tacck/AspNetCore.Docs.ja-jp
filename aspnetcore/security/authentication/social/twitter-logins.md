---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: da0c5579b0828aee7f1c78ec7f5731db50151e90
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634060"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core を使用した Twitter の外部サインインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。

## <a name="create-the-app-in-twitter"></a>Twitter でアプリを作成する

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet パッケージをプロジェクトに追加します。

* に移動し [https://apps.twitter.com/](https://apps.twitter.com/) てサインインします。 まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。

* [ **アプリの作成**] を選択します。 **アプリ名**、**アプリケーションの説明**、およびパブリック**web サイト**の URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。

* [ **Twitter でのサインインを有効**にする] の横にあるチェックボックスをオンにします。

* AspNetCore。Identity 既定では、ユーザーは電子メールアドレスを持っている必要があります。 [ **アクセス許可** ] タブにアクセスし、[ **編集** ] ボタンをクリックして、[ **ユーザーに電子メールアドレスを要求**する] の横にあるチェックボックスをオンにします。

* `/signin-twitter`[**コールバック url** ] フィールドに追加された開発 URI を入力します (例: `https://webapp128.azurewebsites.net/signin-twitter` )。 このサンプルで後で構成される Twitter 認証スキームは、OAuth フローを実装するために、ルートで要求を自動的に処理し `/signin-twitter` ます。

  > [!NOTE]
  > URI セグメント `/signin-twitter` は、Twitter 認証プロバイダーの既定のコールバックとして設定されます。 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* フォームの残りの部分を入力し、[ **作成**] を選択します。 新しいアプリケーションの詳細が表示されます。

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Twitter コンシューマー API キーとシークレットを格納する

Twitter コンシューマー API キーや secret [Manager](xref:security/app-secrets)とシークレットなどの機微な設定を格納します。 このサンプルでは、次の手順を使用します。

1. 「 [シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。
1. 秘密キーとシークレットキーを使用して、機密設定をローカルシークレットストアに保存 `Authentication:Twitter:ConsumerKey` し `Authentication:Twitter:ConsumerSecret` ます。

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

これらのトークンは、新しい Twitter アプリケーションを作成した後の [ **キーとアクセストークン** ] タブにあります。

## <a name="configure-twitter-authentication"></a>Twitter 認証の構成

`ConfigureServices` *Startup.cs*ファイルのメソッドに Twitter サービスを追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求するために使用できます。

## <a name="sign-in-with-twitter"></a>Twitter でのサインイン

アプリを実行し、[ **ログイン**] を選択します。 Twitter でサインインするためのオプションが表示されます。

**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。

Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。

これで、Twitter の資格情報を使用してログインします。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ:**Identityでを呼び出すことによって構成されていない場合 `services.AddIdentity` `ConfigureServices` 、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 初期移行を適用してサイトデータベースが作成されていない場合は、 *要求エラーの処理中にデータベース操作が失敗* します。 [ **移行の適用** ] をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、Twitter で認証する方法について説明しました。 同様のアプローチに従って、 [前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、 `ConsumerSecret` Twitter 開発者ポータルでをリセットする必要があります。

* とを `Authentication:Twitter:ConsumerKey` 、 `Authentication:Twitter:ConsumerSecret` Azure portal のアプリケーション設定として設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
