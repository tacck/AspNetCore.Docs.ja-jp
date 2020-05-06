---
title: ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする
author: rick-anderson
description: ASP.NET Core 2 要素認証で動作する TOTP authenticator アプリの QR コード生成を有効にする方法について説明します。
ms.author: riande
ms.date: 08/14/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 42ddddeaa329ac5ff5b2b40cbf9ebffa68f6d4cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774432"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする

::: moniker range="<= aspnetcore-2.0"

QR コードには ASP.NET Core 2.0 以降が必要です。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core には、個々の認証用の authenticator アプリケーションのサポートが付属しています。 2要素認証 (2FA) 認証アプリは、時間ベースのワンタイムパスワードアルゴリズム (TOTP) を使用して、2FA に業界で推奨されるアプローチです。 TOTP を使用した2FA は、SMS 2FA に適しています。 Authenticator アプリには、ユーザー名とパスワードを確認した後に入力する必要がある 6 ~ 8 桁のコードが用意されています。 通常、認証アプリはスマートフォンにインストールされます。

ASP.NET Core web アプリテンプレートでは認証子がサポートされていますが、QRCode の生成はサポートされていません。 QRCode ジェネレーターにより、2FA のセットアップが簡単になります。 このドキュメントでは、[2FA] 構成ページに[QR コード](https://wikipedia.org/wiki/QR_code)生成を追加する手順について説明します。

[Google](xref:security/authentication/google-logins)や[Facebook](xref:security/authentication/facebook-logins)などの外部認証プロバイダーを使用した場合、2要素認証は行われません。 外部ログインは、外部ログインプロバイダーによって提供されるメカニズムによって保護されます。 たとえば、 [Microsoft](xref:security/authentication/microsoft-logins)認証プロバイダーでは、ハードウェアキーまたは別の2fa アプローチが必要です。 既定のテンプレートで "ローカル" 2FA が適用されている場合は、ユーザーは2つの2FA アプローチを満たす必要があります。これは、一般的には使用されないシナリオです。

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>2FA 構成ページへの QR コードの追加

この手順で*qrcode.js*は、 https://davidshimjs.github.io/qrcodejs/リポジトリから qrcode を使用します。

* [Qrcode javascript ライブラリ](https://davidshimjs.github.io/qrcodejs/)をプロジェクトの`wwwroot\lib`フォルダーにダウンロードします。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* [ Identityスキャフォールディング](xref:security/authentication/scaffold-identity)に記載されている手順に従って、 */Areas//Identity* を生成します。
* */Areas/Identity* で、ファイルの最後にある`Scripts`セクションを見つけます (& a)。この場合は、次の手順を実行します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* *Pages/Account/Manage/enableauthenticator. cshtml* (Razor pages) または*Views/manage/enableauthenticator* (MVC) で、ファイルの末尾`Scripts`にあるセクションを探します。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* `Scripts`セクションを更新して、追加した`qrcodejs`ライブラリへの参照と、QR コードを生成するための呼び出しを追加します。 次のようになります。

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* 次の手順にリンクする段落を削除します。

アプリを実行して、QR コードをスキャンし、認証子が証明するコードを検証できることを確認します。

## <a name="change-the-site-name-in-the-qr-code"></a>QR コードのサイト名を変更する

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。 これを変更するには、 `GenerateQrCodeUri(string email, string unformattedKey)` */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*のメソッドを検索します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。 これを変更するには、 `GenerateQrCodeUri(string email, string unformattedKey)` *pages/Account/Manage/enableauthenticator. cshtml* (Razor pages) ファイルまたは*Controllers/ManageController* (MVC) ファイルのメソッドを検索します。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

テンプレートの既定のコードは、次のようになります。

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

の呼び出しの2番目の`string.Format`パラメーターは、ソリューション名から取得したサイト名です。 任意の値に変更できますが、常に URL エンコードされている必要があります。

## <a name="using-a-different-qr-code-library"></a>別の QR コードライブラリの使用

QR コードライブラリを任意のライブラリに置き換えることができます。 HTML には、 `qrCode`ライブラリが提供するメカニズムによって QR コードを配置できる要素が含まれています。

QR コード用に正しく書式設定された URL は、で入手できます。

* `AuthenticatorUri`モデルのプロパティ。
* `data-url``qrCodeData`要素のプロパティ。

## <a name="totp-client-and-server-time-skew"></a>TOTP クライアントとサーバーの時刻のずれ

TOTP (時間ベースのワンタイムパスワード) 認証は、サーバーと authenticator デバイスの正確な時刻の両方に依存します。 トークンは30秒間のみ最後にあります。 TOTP 2FA ログインが失敗した場合は、サーバーの時刻が正確であること、および適切な NTP サービスに同期されていることを確認します。

::: moniker-end
