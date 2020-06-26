---
title: ASP.NET Core でのアカウントの確認とパスワードの回復
author: rick-anderson
description: 電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404653"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="5602d-103">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="5602d-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="5602d-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)、 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="5602d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="5602d-105">このチュートリアルでは、電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5602d-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="5602d-106">このチュートリアルは最初のトピックでは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="5602d-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="5602d-107">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-107">You should be familiar with:</span></span>

* [<span data-ttu-id="5602d-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5602d-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="5602d-109">認証</span><span class="sxs-lookup"><span data-stu-id="5602d-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5602d-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5602d-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5602d-111">ASP.NET Core 1.1 バージョンについては、[この PDF ファイル](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="5602d-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5602d-112">Prerequisites</span></span>

[<span data-ttu-id="5602d-113">.NET Core 3.0 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="5602d-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="5602d-114">認証を使用した web アプリの作成とテスト</span><span class="sxs-lookup"><span data-stu-id="5602d-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="5602d-115">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5602d-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="5602d-116">アプリを実行し、[**登録**] リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5602d-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="5602d-117">登録されると、 `/Identity/Account/RegisterConfirmation` 電子メールの確認をシミュレートするためのリンクを含む [to] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="5602d-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="5602d-118">リンクを選択し `Click here to confirm your account` ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="5602d-119">**ログイン**リンクを選択し、同じ資格情報でサインインします。</span><span class="sxs-lookup"><span data-stu-id="5602d-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="5602d-120">リンクを選択 `Hello YourEmail@provider.com!` すると、ページにリダイレクトされ `/Identity/Account/Manage/PersonalData` ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="5602d-121">左側の [ **Personal data** ] タブを選択し、[**削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5602d-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="5602d-122">電子メールプロバイダーを構成する</span><span class="sxs-lookup"><span data-stu-id="5602d-122">Configure an email provider</span></span>

<span data-ttu-id="5602d-123">このチュートリアルでは、 [Sendgrid](https://sendgrid.com)を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="5602d-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="5602d-124">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="5602d-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="5602d-125">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-125">You can use other email providers.</span></span> <span data-ttu-id="5602d-126">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5602d-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="5602d-127">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="5602d-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="5602d-128">SendGrid アカウントでは、[送信者の追加](https://sendgrid.com/docs/ui/sending-email/senders/)が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="5602d-129">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5602d-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="5602d-130">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="5602d-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="5602d-131">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="5602d-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="5602d-132">とを `SendGridUser` 、 `SendGridKey` [シークレットマネージャーツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="5602d-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5602d-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5602d-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="5602d-134">Windows では、シークレットマネージャーは、キーと値のペアをディレクトリ内のファイル*のsecrets.js*に格納 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` します。</span><span class="sxs-lookup"><span data-stu-id="5602d-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="5602d-135">ファイルの*secrets.js*の内容が暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="5602d-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="5602d-136">次のマークアップは、ファイル*のsecrets.js*を示しています。</span><span class="sxs-lookup"><span data-stu-id="5602d-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="5602d-137">`SendGridKey`値は削除されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="5602d-138">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="5602d-139">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="5602d-139">Install SendGrid</span></span>

<span data-ttu-id="5602d-140">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="5602d-141">NuGet パッケージをインストールし `SendGrid` ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5602d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5602d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5602d-143">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5602d-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5602d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5602d-145">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="5602d-146">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="5602d-147">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="5602d-147">Implement IEmailSender</span></span>

<span data-ttu-id="5602d-148">を実装するには `IEmailSender` 、次のようなコードを使用して*サービス/emailsender*を作成します。</span><span class="sxs-lookup"><span data-stu-id="5602d-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="5602d-149">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="5602d-149">Configure startup to support email</span></span>

<span data-ttu-id="5602d-150">Startup.cs ファイルのメソッドに次のコードを追加し `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5602d-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="5602d-151">`EmailSender`を一時サービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="5602d-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="5602d-152">`AuthMessageSenderOptions`構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="5602d-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="5602d-153">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="5602d-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="5602d-154">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="5602d-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="5602d-155">アプリを実行して新しいユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="5602d-155">Run the app and register a new user</span></span>
* <span data-ttu-id="5602d-156">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="5602d-157">電子メールを受信しない場合は、「[デバッグ電子メール](#debug)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="5602d-158">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="5602d-159">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="5602d-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="5602d-160">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="5602d-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="5602d-161">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="5602d-161">Test password reset</span></span>

* <span data-ttu-id="5602d-162">サインインしている場合は、[**ログアウト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5602d-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="5602d-163">[**ログイン**] リンクを選択し、[**パスワードを忘れ**た場合] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5602d-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="5602d-164">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="5602d-165">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="5602d-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="5602d-166">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="5602d-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="5602d-167">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="5602d-168">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-168">Change email and activity timeout</span></span>

<span data-ttu-id="5602d-169">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="5602d-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="5602d-170">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="5602d-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="5602d-171">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="5602d-172">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="5602d-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="5602d-173">組み込みの Identity ユーザートークン (「 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には[1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="5602d-174">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-174">Change the email token lifespan</span></span>

<span data-ttu-id="5602d-175">[ Identity ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="5602d-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="5602d-176">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5602d-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="5602d-177">カスタム[ \<TUser> DataProtectorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)とを追加し <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="5602d-178">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="5602d-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="5602d-179">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="5602d-179">Resend email confirmation</span></span>

<span data-ttu-id="5602d-180">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="5602d-181">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="5602d-181">Debug email</span></span>

<span data-ttu-id="5602d-182">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="5602d-182">If you can't get email working:</span></span>

* <span data-ttu-id="5602d-183">にブレークポイントを設定し `EmailSender.Execute` て、 `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="5602d-184">同様のコードを使用して[電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)を作成 `EmailSender.Execute` します。</span><span class="sxs-lookup"><span data-stu-id="5602d-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="5602d-185">[[電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html)] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="5602d-186">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-186">Check your spam folder.</span></span>
* <span data-ttu-id="5602d-187">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="5602d-188">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="5602d-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="5602d-189">**セキュリティのベストプラクティス**として、テストと開発では運用シークレットを使用し**ないこと**をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5602d-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="5602d-190">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="5602d-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="5602d-191">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="5602d-192">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="5602d-192">Combine social and local login accounts</span></span>

<span data-ttu-id="5602d-193">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="5602d-194">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5602d-195">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="5602d-196">次のシーケンスでは、" RickAndMSFT@gmail.com " は最初にローカルログインとして作成されますが、最初にアカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="5602d-198">[**管理**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="5602d-198">Click on the **Manage** link.</span></span> <span data-ttu-id="5602d-199">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-199">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="5602d-201">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="5602d-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="5602d-202">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="5602d-202">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="5602d-204">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-204">The two accounts have been combined.</span></span> <span data-ttu-id="5602d-205">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-205">You are able to sign in with either account.</span></span> <span data-ttu-id="5602d-206">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="5602d-207">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="5602d-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="5602d-208">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="5602d-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="5602d-209">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="5602d-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="5602d-210">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="5602d-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="5602d-211">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="5602d-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="5602d-212">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-212">Confirm existing users.</span></span> <span data-ttu-id="5602d-213">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="5602d-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5602d-214">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5602d-214">Prerequisites</span></span>

[<span data-ttu-id="5602d-215">.NET Core 2.2 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="5602d-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="5602d-216">Web アプリとスキャフォールディングを作成するIdentity</span><span class="sxs-lookup"><span data-stu-id="5602d-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="5602d-217">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5602d-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="5602d-218">新しいユーザー登録をテストする</span><span class="sxs-lookup"><span data-stu-id="5602d-218">Test new user registration</span></span>

<span data-ttu-id="5602d-219">アプリを実行し、[**登録**] リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="5602d-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="5602d-220">この時点で、電子メールに対する検証は、属性を使用した場合のみです [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 。</span><span class="sxs-lookup"><span data-stu-id="5602d-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="5602d-221">登録を送信すると、アプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="5602d-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="5602d-222">このチュートリアルの後半では、電子メールが検証されるまで新しいユーザーがサインインできないように、コードが更新されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="5602d-223">テーブルの `EmailConfirmed` フィールドがであることに注意 `False` してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="5602d-224">アプリが確認の電子メールを送信するときに、次の手順でもう一度このメールを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="5602d-225">行を右クリックし、[**削除**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="5602d-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="5602d-226">電子メールエイリアスを削除すると、次の手順が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="5602d-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="5602d-227">電子メールの確認が必要</span><span class="sxs-lookup"><span data-stu-id="5602d-227">Require email confirmation</span></span>

<span data-ttu-id="5602d-228">新しいユーザー登録の電子メールを確認することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5602d-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="5602d-229">電子メールの確認では、他のユーザーの権限を借用していないこと (つまり、他のユーザーの電子メールに登録されていないこと) を確認できます。</span><span class="sxs-lookup"><span data-stu-id="5602d-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="5602d-230">ディスカッションフォーラムを使用していて、"" を "" として登録できないようにしたいとし yli@example.com nolivetto@contoso.com ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="5602d-231">電子メールを確認しない場合、" nolivetto@contoso.com " はアプリから不要な電子メールを受信する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="5602d-232">ユーザーが誤って "" として登録され、 ylo@example.com "yli" のスペルミスに気付かないとします。</span><span class="sxs-lookup"><span data-stu-id="5602d-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="5602d-233">アプリには正しい電子メールがないため、パスワードの回復を使用できません。</span><span class="sxs-lookup"><span data-stu-id="5602d-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="5602d-234">電子メールの確認では、ボットからの保護が制限されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="5602d-235">電子メールの確認では、多くの電子メールアカウントを持つ悪意のあるユーザーからの保護は提供されません。</span><span class="sxs-lookup"><span data-stu-id="5602d-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="5602d-236">通常は、確認済みの電子メールを送信する前に、新しいユーザーが web サイトにデータを投稿できないようにします。</span><span class="sxs-lookup"><span data-stu-id="5602d-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="5602d-237">`Startup.ConfigureServices`確認済みの電子メールを要求するように更新します。</span><span class="sxs-lookup"><span data-stu-id="5602d-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="5602d-238">`config.SignIn.RequireConfirmedEmail = true;`登録されたユーザーが電子メールを確認するまでログインできないようにします。</span><span class="sxs-lookup"><span data-stu-id="5602d-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="5602d-239">電子メールプロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="5602d-239">Configure email provider</span></span>

<span data-ttu-id="5602d-240">このチュートリアルでは、 [Sendgrid](https://sendgrid.com)を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="5602d-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="5602d-241">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="5602d-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="5602d-242">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-242">You can use other email providers.</span></span> <span data-ttu-id="5602d-243">ASP.NET Core 2.x `System.Net.Mail` には、アプリから電子メールを送信できるようにするが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5602d-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="5602d-244">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5602d-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="5602d-245">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="5602d-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="5602d-246">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5602d-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="5602d-247">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="5602d-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="5602d-248">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="5602d-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="5602d-249">とを `SendGridUser` 、 `SendGridKey` [シークレットマネージャーツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="5602d-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5602d-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5602d-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="5602d-251">Windows では、シークレットマネージャーは、キーと値のペアをディレクトリ内のファイル*のsecrets.js*に格納 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` します。</span><span class="sxs-lookup"><span data-stu-id="5602d-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="5602d-252">ファイルの*secrets.js*の内容が暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="5602d-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="5602d-253">次のマークアップは、ファイル*のsecrets.js*を示しています。</span><span class="sxs-lookup"><span data-stu-id="5602d-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="5602d-254">`SendGridKey`値は削除されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="5602d-255">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="5602d-256">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="5602d-256">Install SendGrid</span></span>

<span data-ttu-id="5602d-257">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="5602d-258">NuGet パッケージをインストールし `SendGrid` ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5602d-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5602d-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5602d-260">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5602d-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5602d-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5602d-262">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="5602d-263">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="5602d-264">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="5602d-264">Implement IEmailSender</span></span>

<span data-ttu-id="5602d-265">を実装するには `IEmailSender` 、次のようなコードを使用して*サービス/emailsender*を作成します。</span><span class="sxs-lookup"><span data-stu-id="5602d-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="5602d-266">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="5602d-266">Configure startup to support email</span></span>

<span data-ttu-id="5602d-267">Startup.cs ファイルのメソッドに次のコードを追加し `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5602d-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="5602d-268">`EmailSender`を一時サービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="5602d-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="5602d-269">`AuthMessageSenderOptions`構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="5602d-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="5602d-270">アカウントの確認とパスワードの回復を有効にする</span><span class="sxs-lookup"><span data-stu-id="5602d-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="5602d-271">このテンプレートには、アカウントの確認とパスワードの回復のためのコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5602d-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="5602d-272">`OnPostAsync` *Areas/ Identity /Pages/Account/Register.cshtml.cs*でメソッドを検索します。</span><span class="sxs-lookup"><span data-stu-id="5602d-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="5602d-273">新しく登録されたユーザーが自動的にサインインしないようにするには、次の行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="5602d-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="5602d-274">完全なメソッドは、変更された行が強調表示された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="5602d-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="5602d-275">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="5602d-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="5602d-276">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="5602d-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="5602d-277">アプリを実行して新しいユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="5602d-277">Run the app and register a new user</span></span>
* <span data-ttu-id="5602d-278">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="5602d-279">電子メールを受信しない場合は、「[デバッグ電子メール](#debug)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="5602d-280">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="5602d-281">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="5602d-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="5602d-282">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="5602d-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="5602d-283">[管理] ページを表示する</span><span class="sxs-lookup"><span data-stu-id="5602d-283">View the manage page</span></span>

<span data-ttu-id="5602d-284">ブラウザーでユーザー名を選択します: ![ ユーザー名が表示されたブラウザーウィンドウ](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="5602d-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="5602d-285">[管理] ページが、[**プロファイル**] タブが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="5602d-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="5602d-286">電子メールには、電子メールが確認されたことを示すチェックボックス**が表示さ**れます。</span><span class="sxs-lookup"><span data-stu-id="5602d-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="5602d-287">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="5602d-287">Test password reset</span></span>

* <span data-ttu-id="5602d-288">サインインしている場合は、[**ログアウト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5602d-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="5602d-289">[**ログイン**] リンクを選択し、[**パスワードを忘れ**た場合] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="5602d-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="5602d-290">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="5602d-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="5602d-291">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="5602d-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="5602d-292">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="5602d-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="5602d-293">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="5602d-294">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-294">Change email and activity timeout</span></span>

<span data-ttu-id="5602d-295">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="5602d-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="5602d-296">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="5602d-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="5602d-297">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="5602d-298">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="5602d-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="5602d-299">組み込みの Identity ユーザートークン (「 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には[1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="5602d-300">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="5602d-300">Change the email token lifespan</span></span>

<span data-ttu-id="5602d-301">[ Identity ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="5602d-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="5602d-302">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5602d-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="5602d-303">カスタム[ \<TUser> DataProtectorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)とを追加し <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="5602d-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="5602d-304">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="5602d-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="5602d-305">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="5602d-305">Resend email confirmation</span></span>

<span data-ttu-id="5602d-306">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="5602d-307">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="5602d-307">Debug email</span></span>

<span data-ttu-id="5602d-308">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="5602d-308">If you can't get email working:</span></span>

* <span data-ttu-id="5602d-309">にブレークポイントを設定し `EmailSender.Execute` て、 `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="5602d-310">同様のコードを使用して[電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)を作成 `EmailSender.Execute` します。</span><span class="sxs-lookup"><span data-stu-id="5602d-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="5602d-311">[[電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html)] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="5602d-312">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-312">Check your spam folder.</span></span>
* <span data-ttu-id="5602d-313">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="5602d-314">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="5602d-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="5602d-315">**セキュリティのベストプラクティス**として、テストと開発では運用シークレットを使用し**ないこと**をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5602d-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="5602d-316">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定できます。</span><span class="sxs-lookup"><span data-stu-id="5602d-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="5602d-317">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="5602d-318">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="5602d-318">Combine social and local login accounts</span></span>

<span data-ttu-id="5602d-319">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5602d-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="5602d-320">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5602d-321">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="5602d-322">次のシーケンスでは、" RickAndMSFT@gmail.com " は最初にローカルログインとして作成されますが、最初にアカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="5602d-324">[**管理**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="5602d-324">Click on the **Manage** link.</span></span> <span data-ttu-id="5602d-325">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="5602d-325">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="5602d-327">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="5602d-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="5602d-328">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="5602d-328">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="5602d-330">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="5602d-330">The two accounts have been combined.</span></span> <span data-ttu-id="5602d-331">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="5602d-331">You are able to sign in with either account.</span></span> <span data-ttu-id="5602d-332">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5602d-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="5602d-333">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="5602d-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="5602d-334">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="5602d-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="5602d-335">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="5602d-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="5602d-336">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="5602d-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="5602d-337">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="5602d-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="5602d-338">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5602d-338">Confirm existing users.</span></span> <span data-ttu-id="5602d-339">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="5602d-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
