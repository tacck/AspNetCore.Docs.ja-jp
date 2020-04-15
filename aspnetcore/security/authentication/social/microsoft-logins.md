---
title: ASP.NET コアを使用した Microsoft アカウント外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384039"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET コアを使用した Microsoft アカウント外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが職場、学校、または個人の Microsoft アカウントでサインインできるようにする方法を示します。

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 開発者ポータルでアプリを作成する

* プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/)追加します。
* [Azure ポータル - アプリ登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。

Microsoft アカウントをお持ちの場合は、[**アカウントを作成**する] を選択します。 サインイン後、**アプリの登録**ページにリダイレクトされます。

* **新規登録の**選択
* **[名前]** を入力します。
* [**サポートされているアカウントの種類]** のオプションを選択します。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* [**リダイレクト URI]** に、開発`/signin-microsoft`用 URL を追加して入力します。 たとえば、「 `https://localhost:5001/signin-microsoft` 」のように入力します。 このサンプルで後述する Microsoft 認証スキームは、OAuth フローを実装するルートで`/signin-microsoft`要求を自動的に処理します。
* **[登録]** を選択します

### <a name="create-client-secret"></a>クライアント シークレットを作成する

* 左側のウィンドウで、**[証明書とシークレット]** を選択します。
* [**クライアント シークレット**] で 、[**新しいクライアント シークレット**] を選択します。

  * クライアント シークレットの説明を追加します。
  * [**追加**] ボタンを選択します。

* [**クライアント シークレット**] の下で、クライアント シークレットの値をコピーします。

URI セグメント`/signin-microsoft`は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。 クラスの継承された[リモート認証オプション.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用してマイクロソフト認証ミドル[ウェアを構成](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)する際に、既定のコールバック URI を変更できます。

## <a name="store-the-microsoft-client-id-and-secret"></a>マイクロソフトのクライアント ID とシークレットを保存する

秘密マネージャー を使用して、Microsoft クライアント ID やシークレット値などの機密性の高い設定を保存[します](xref:security/app-secrets)。 このサンプルでは、次の手順を実行します。

1. 「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。
1. 秘密鍵とローカルシークレットストアに機密設定を保存`Authentication:Microsoft:ClientId`し、 `Authentication:Microsoft:ClientSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>マイクロソフト アカウント認証の構成

に Microsoft アカウント サービス`Startup.ConfigureServices`を追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft アカウント認証でサポートされる構成オプションの詳細については[、API](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求するために使用できます。

## <a name="sign-in-with-microsoft-account"></a>マイクロソフト アカウントでサインインする

アプリを実行し、[**ログイン**] をクリックします。 マイクロソフトでサインインするオプションが表示されます。 マイクロソフトをクリックすると、認証のためにマイクロソフトにリダイレクトされます。 Microsoft アカウントでサインインすると、アプリが情報にアクセスするように求められます。

[**はい**]をタップすると、メールを設定できるWebサイトにリダイレクトされます。

これで、Microsoft の資格情報を使用してログインします。

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* Microsoft アカウント プロバイダーがサインイン エラー ページにリダイレクトする場合は、URI の (ハッシュタグ) の直後`#`にエラー のタイトルと説明のクエリ文字列パラメーターをメモします。

  エラー メッセージは Microsoft 認証の問題を示しているように見えますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定された**リダイレクト URI のいずれ**にも一致しないことです。
* で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。 このサンプルで使用されているプロジェクト テンプレートによって、この処理が確実に行われます。
* 初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。 [**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、マイクロソフトで認証を行う方法について説明しました。 同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。

* Web サイトを Azure Web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアント シークレットを作成します。

* と`Authentication:Microsoft:ClientId`として`Authentication:Microsoft:ClientSecret`アプリケーションの設定を Azure ポータルで設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
