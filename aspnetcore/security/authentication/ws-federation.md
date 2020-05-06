---
title: ASP.NET Core で WS-FEDERATION を使用してユーザーを認証する
author: chlowell
description: このチュートリアルでは、ASP.NET Core アプリで WS-FEDERATION を使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: fede3887ad7dacd40cf3bb5d1b785392a9bc1480
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850462"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>ASP.NET Core で WS-FEDERATION を使用してユーザーを認証する

このチュートリアルでは、ユーザーが Active Directory フェデレーションサービス (AD FS) (ADFS) や[Azure Active Directory](/azure/active-directory/) (AAD) などの ws-federation 認証プロバイダーを使用してサインインできるようにする方法について説明します。 [Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)で説明されている ASP.NET Core サンプルアプリを使用します。

ASP.NET Core アプリの場合、WS-FEDERATION のサポートは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)によって提供されます。 このコンポーネントは[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)から移植され、そのコンポーネントの機構の多くを共有します。 ただし、これらのコンポーネントは、いくつかの重要な点で異なります。

既定では、新しいミドルウェアは次のようになります。

* は、要求されていないログインを許可しません。 WS-FEDERATION プロトコルのこの機能は、XSRF 攻撃に対して脆弱です。 ただし、 `AllowUnsolicitedLogins`オプションを使用して有効にすることができます。
* では、サインインメッセージのすべてのフォームポストがチェックされません。 に対する要求のみ`CallbackPath`がサインインのチェックが行われ`CallbackPath`ます。 `/signin-wsfed`既定値はですが、 [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions)クラスの継承された[remoteauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)を使用して変更できます。 このパスは、 [Skipun認識要求](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests)オプションを有効にすることで、他の認証プロバイダーと共有できます。

## <a name="register-the-app-with-active-directory"></a>Active Directory にアプリを登録する

### <a name="active-directory-federation-services"></a>Active Directory フェデレーション サービス

* ADFS 管理コンソールから、サーバーの**証明書利用者信頼の追加ウィザード**を開きます。

![証明書利用者信頼の追加ウィザード: ようこそ](ws-federation/_static/AdfsAddTrust.png)

* データを手動で入力する場合に選択します。

![証明書利用者信頼の追加ウィザード: データソースの選択](ws-federation/_static/AdfsSelectDataSource.png)

* 証明書利用者の表示名を入力します。 名前は ASP.NET Core アプリにとって重要ではありません。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)では、トークンの暗号化がサポートされていないため、トークン暗号化証明書を構成しないでください。

![証明書利用者信頼の追加ウィザード: 証明書の構成](ws-federation/_static/AdfsConfigureCert.png)

* アプリの URL を使用して、WS-FEDERATION パッシブプロトコルのサポートを有効にします。 アプリケーションのポートが正しいことを確認します。

![証明書利用者信頼の追加ウィザード: URL の構成](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> これは HTTPS URL である必要があります。 IIS Express は、開発時にアプリをホストするときに自己署名証明書を提供できます。 Kestrel には、手動による証明書の構成が必要です。 詳細については、 [Kestrel のドキュメント](xref:fundamentals/servers/kestrel)を参照してください。

* ウィザードの残りの部分で [**次へ**] をクリックし、最後**に終了し**ます。

* ASP.NET Core Identityには**名前 ID**要求が必要です。 [**要求規則の編集**] ダイアログボックスで、次のいずれかを追加します。

![要求規則を編集する](ws-federation/_static/EditClaimRules.png)

* **変換要求規則の追加ウィザード**で、既定の [ **LDAP 属性を要求として送信**] テンプレートを選択したままにして、[**次へ**] をクリックします。 **SAM-Account-name** LDAP 属性を出力方向の要求の**名前 ID**にマッピングする規則を追加します。

![変換要求規則の追加ウィザード: 要求規則の構成](ws-federation/_static/AddTransformClaimRule.png)

* [**要求規則の編集**] ウィンドウで [**完了** > **]** をクリックします。

### <a name="azure-active-directory"></a>Azure Active Directory

* AAD テナントの [アプリの登録] ブレードに移動します。 [**新しいアプリケーションの登録**] をクリックします。

![Azure Active Directory: アプリの登録](ws-federation/_static/AadNewAppRegistration.png)

* アプリ登録の名前を入力します。 これは、ASP.NET Core アプリにとって重要ではありません。
* **サインオン url**としてアプリがリッスンする url を入力してください:

![Azure Active Directory: アプリの登録を作成する](ws-federation/_static/AadCreateAppRegistration.png)

* [**エンドポイント**] をクリックして、**フェデレーションメタデータドキュメント**の URL を確認します。 WS-FEDERATION ミドルウェアは`MetadataAddress`次のようになります。

![Azure Active Directory: エンドポイント](ws-federation/_static/AadFederationMetadataDocument.png)

* 新しいアプリの登録に移動します。 [**設定** > の**プロパティ**] をクリックし、**アプリ ID URI**をメモします。 WS-FEDERATION ミドルウェアは`Wtrealm`次のようになります。

![Azure Active Directory: アプリの登録のプロパティ](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>ASP.NET Core せずに WS-FEDERATION を使用するIdentity

WS-FEDERATION ミドルウェアは、なしでIdentity使用できます。 次に例を示します。
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>ASP.NET Core の外部ログインプロバイダーとして WS-FEDERATION を追加します。Identity

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)への依存関係をプロジェクトに追加します。
* WS-FEDERATION の追加先`Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>WS-FEDERATION を使用してログインする

アプリを参照し、nav ヘッダーの [**ログイン**] リンクをクリックします。 WsFederation: ログインページでログインするオプションがあり![ます。](ws-federation/_static/WsFederationButton.png)

ADFS をプロバイダーとして使用すると、このボタンは adfs サインインページに![リダイレクトされます。 adfs サインインページ](ws-federation/_static/AdfsLoginPage.png)

プロバイダーとして Azure Active Directory を使用すると、このボタンは AAD サインインページに![リダイレクトされます。 aad サインインページ](ws-federation/_static/AadSignIn.png)

新しいユーザーのサインインが成功すると、アプリのユーザー登録ページにリダイレクトされ![ます。 [登録] ページ](ws-federation/_static/Register.png)
