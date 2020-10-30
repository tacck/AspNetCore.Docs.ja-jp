---
title: ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする
author: rick-anderson
description: ASP.NET Core 2 要素認証で動作する TOTP authenticator アプリの QR コード生成を有効にする方法について説明します。
ms.author: riande
ms.date: 08/14/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060834"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="aa6dd-103">ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする</span><span class="sxs-lookup"><span data-stu-id="aa6dd-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aa6dd-104">QR コードには ASP.NET Core 2.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aa6dd-105">ASP.NET Core には、個々の認証用の authenticator アプリケーションのサポートが付属しています。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="aa6dd-106">2要素認証 (2FA) 認証アプリは、時間ベースのワンタイムパスワードアルゴリズム (TOTP) を使用して、2FA に業界で推奨されるアプローチです。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="aa6dd-107">TOTP を使用した2FA は、SMS 2FA に適しています。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="aa6dd-108">Authenticator アプリには、ユーザー名とパスワードを確認した後に入力する必要がある 6 ~ 8 桁のコードが用意されています。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="aa6dd-109">通常、認証アプリはスマートフォンにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="aa6dd-110">ASP.NET Core web アプリテンプレートでは認証子がサポートされていますが、QRCode の生成はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="aa6dd-111">QRCode ジェネレーターにより、2FA のセットアップが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="aa6dd-112">このドキュメントでは、[2FA] 構成ページに [QR コード](https://wikipedia.org/wiki/QR_code) 生成を追加する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="aa6dd-113">[Google](xref:security/authentication/google-logins)や[Facebook](xref:security/authentication/facebook-logins)などの外部認証プロバイダーを使用した場合、2要素認証は行われません。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="aa6dd-114">外部ログインは、外部ログインプロバイダーによって提供されるメカニズムによって保護されます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="aa6dd-115">たとえば、 [Microsoft](xref:security/authentication/microsoft-logins) 認証プロバイダーでは、ハードウェアキーまたは別の2fa アプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="aa6dd-116">既定のテンプレートで "ローカル" 2FA が適用されている場合は、ユーザーは2つの2FA アプローチを満たす必要があります。これは、一般的には使用されないシナリオです。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="aa6dd-117">2FA 構成ページへの QR コードの追加</span><span class="sxs-lookup"><span data-stu-id="aa6dd-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="aa6dd-118">この手順では、リポジトリの *qrcode.js* を使用し https://davidshimjs.github.io/qrcodejs/ ます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="aa6dd-119">[qrcode.js javascript ライブラリ](https://davidshimjs.github.io/qrcodejs/)を `wwwroot\lib` プロジェクトのフォルダーにダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="aa6dd-120">[ :::no-loc(Identity)::: スキャフォールディング](xref:security/authentication/scaffold-identity)に記載されている手順に従って、/Areas//を生成します。 *:::no-loc(Identity):::*</span><span class="sxs-lookup"><span data-stu-id="aa6dd-120">Follow the instructions in [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) to generate */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="aa6dd-121">/Areas/で、ファイルの最後にあるセクションを見つけます (& a)。この場合は、次の手順を *:::no-loc(Identity):::* `Scripts` 実行します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-121">In */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="aa6dd-122">*Pages/Account/Manage/EnableAuthenticator. cshtml* ( :::no-loc(Razor)::: pages) または *Views/manage/enableauthenticator* (MVC) で、 `Scripts` ファイルの末尾にあるセクションを探します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (:::no-loc(Razor)::: Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="aa6dd-123">セクションを更新して、 `Scripts` 追加したライブラリへの参照 `qrcodejs` と、QR コードを生成するための呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="aa6dd-124">次のようになります。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-124">It should look as follows:</span></span>

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

* <span data-ttu-id="aa6dd-125">次の手順にリンクする段落を削除します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="aa6dd-126">アプリを実行して、QR コードをスキャンし、認証子が証明するコードを検証できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="aa6dd-127">QR コードのサイト名を変更する</span><span class="sxs-lookup"><span data-stu-id="aa6dd-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="aa6dd-128">QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="aa6dd-129">これを変更するには `GenerateQrCodeUri(string email, string unformattedKey)` 、 */Areas/ :::no-loc(Identity)::: /Pages/Account/Manage/EnableAuthenticator.cshtml.cs* のメソッドを検索します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="aa6dd-130">QR コードのサイト名は、プロジェクトを最初に作成するときに選択したプロジェクト名から取得されます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="aa6dd-131">これを変更するには `GenerateQrCodeUri(string email, string unformattedKey)` 、 *Pages/Account/Manage/enableauthenticator. cshtml* ( :::no-loc(Razor)::: pages) ファイルまたは *Controllers/ManageController* (MVC) ファイルのメソッドを検索します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (:::no-loc(Razor)::: Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aa6dd-132">テンプレートの既定のコードは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode(":::no-loc(Razor)::: Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="aa6dd-133">の呼び出しの2番目のパラメーター `string.Format` は、ソリューション名から取得したサイト名です。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="aa6dd-134">任意の値に変更できますが、常に URL エンコードされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="aa6dd-135">別の QR コードライブラリの使用</span><span class="sxs-lookup"><span data-stu-id="aa6dd-135">Using a different QR Code library</span></span>

<span data-ttu-id="aa6dd-136">QR コードライブラリを任意のライブラリに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="aa6dd-137">HTML には、 `qrCode` ライブラリが提供するメカニズムによって QR コードを配置できる要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="aa6dd-138">QR コード用に正しく書式設定された URL は、で入手できます。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="aa6dd-139">`AuthenticatorUri` モデルのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="aa6dd-140">`data-url` 要素のプロパティ `qrCodeData` 。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="aa6dd-141">TOTP クライアントとサーバーの時刻のずれ</span><span class="sxs-lookup"><span data-stu-id="aa6dd-141">TOTP client and server time skew</span></span>

<span data-ttu-id="aa6dd-142">TOTP (時間ベースの One-Time パスワード) 認証は、サーバーと authenticator デバイスの正確な時刻の両方に依存します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="aa6dd-143">トークンは30秒間のみ最後にあります。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="aa6dd-144">TOTP 2FA ログインが失敗した場合は、サーバーの時刻が正確であること、および適切な NTP サービスに同期されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="aa6dd-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
