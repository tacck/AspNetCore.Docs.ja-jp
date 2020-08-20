---
title: ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3430d842b6a4f7da30370977f72e6f132e28bb7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634255"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、 [前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが職場、学校、または personal Microsoft アカウントでサインインできるようにする方法を示します。

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 開発者ポータルでアプリを作成する

* プロジェクトに、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet パッケージを追加します。
* [Azure portal アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。

Microsoft アカウントがない場合は、[ **作成**] を選択します。 サインインすると、[ **アプリの登録** ] ページにリダイレクトされます。

* **新しい登録**の選択
* **[名前]** を入力します。
* **サポートされているアカウントの種類**のオプションを選択します。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* [ **リダイレクト URI**] に、追加した開発 URL を入力し `/signin-microsoft` ます。 たとえば、「 `https://localhost:5001/signin-microsoft` 」のように入力します。 このサンプルの後半で構成されている Microsoft 認証スキームは、OAuth フローを実装するために、ルートで要求を自動的に処理し `/signin-microsoft` ます。
* **[登録]** を選択します

### <a name="create-client-secret"></a>クライアント シークレットを作成する

* 左側のウィンドウで、 **[証明書とシークレット]** を選択します。
* [**クライアントシークレット**] で、[**新しいクライアントシークレット**] を選択します。

  * クライアントシークレットの説明を追加します。
  * **[追加]** ボタンを選びます。

* [ **クライアントシークレット**] で、クライアントシークレットの値をコピーします。

URI セグメント `/signin-microsoft` は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Microsoft 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

## <a name="store-the-microsoft-client-id-and-secret"></a>Microsoft クライアント ID とシークレットを保存する

Microsoft のクライアント ID やシークレットの値など、機微な設定を [シークレットマネージャー](xref:security/app-secrets)に保存します。 このサンプルでは、次の手順を使用します。

1. 「 [シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。
1. 秘密キーとシークレットキーを使用して、機密設定をローカルシークレットストアに保存 `Authentication:Microsoft:ClientId` し `Authentication:Microsoft:ClientSecret` ます。

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft アカウント認証を構成する

Microsoft アカウントサービスをに追加し `Startup.ConfigureServices` ます。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft アカウント認証でサポートされる構成オプションの詳細については、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求するために使用できます。

## <a name="sign-in-with-microsoft-account"></a>Microsoft アカウントでサインインアカウント

アプリを実行し、[ **ログイン**] をクリックします。 Microsoft でサインインするためのオプションが表示されます。 [Microsoft] をクリックすると、認証のために Microsoft にリダイレクトされます。 Microsoft アカウントでサインインすると、アプリが情報にアクセスできるようにするように求めるメッセージが表示されます。

**[はい]** をタップすると、電子メールを設定できる web サイトにリダイレクトされます。

これで、Microsoft 資格情報を使用してログインしました。

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* Microsoft アカウントプロバイダーによってサインインエラーページが表示された場合は、 `#` Uri の (ハッシュタグ) のすぐ後にあるエラータイトルと説明のクエリ文字列パラメーターを確認してください。

  エラーメッセージは Microsoft 認証に問題があることを示していますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定されている**リダイレクト uri**と一致していないことです。
* Identityでを呼び出すことによって構成されていない場合 `services.AddIdentity` `ConfigureServices` 、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 初期移行を適用してサイトデータベースが作成されていない場合は、 *要求エラーの処理中にデータベース操作が失敗* します。 [ **移行の適用** ] をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ

* この記事では、Microsoft で認証する方法について説明しました。 同様のアプローチに従って、 [前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアントシークレットを作成します。

* とを `Authentication:Microsoft:ClientId` 、 `Authentication:Microsoft:ClientSecret` Azure portal のアプリケーション設定として設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
