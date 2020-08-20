---
title: ASP.NET Core でのアカウントの確認とパスワードの回復
author: rick-anderson
description: 電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 57607390e7d5e58df9f27437faecd57504ad64df
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635373"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="89539-103">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="89539-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="89539-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)、 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="89539-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="89539-105">このチュートリアルでは、電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89539-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="89539-106">このチュートリアルは最初のトピックでは **ありません** 。</span><span class="sxs-lookup"><span data-stu-id="89539-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="89539-107">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="89539-107">You should be familiar with:</span></span>

* [<span data-ttu-id="89539-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89539-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="89539-109">認証</span><span class="sxs-lookup"><span data-stu-id="89539-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="89539-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="89539-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="89539-111">前提条件</span><span class="sxs-lookup"><span data-stu-id="89539-111">Prerequisites</span></span>

[<span data-ttu-id="89539-112">.NET Core 3.0 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="89539-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="89539-113">認証を使用した web アプリの作成とテスト</span><span class="sxs-lookup"><span data-stu-id="89539-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="89539-114">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89539-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="89539-115">アプリを実行し、[ **登録** ] リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="89539-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="89539-116">登録されると、 `/Identity/Account/RegisterConfirmation` 電子メールの確認をシミュレートするためのリンクを含む [to] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="89539-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="89539-117">[`Click here to confirm your account`]\(ログの一元化\) リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="89539-118">**ログイン**リンクを選択し、同じ資格情報でサインインします。</span><span class="sxs-lookup"><span data-stu-id="89539-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="89539-119">リンクを選択 `Hello YourEmail@provider.com!` すると、ページにリダイレクトされ `/Identity/Account/Manage/PersonalData` ます。</span><span class="sxs-lookup"><span data-stu-id="89539-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="89539-120">左側の [ **Personal data** ] タブを選択し、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="89539-121">電子メールプロバイダーを構成する</span><span class="sxs-lookup"><span data-stu-id="89539-121">Configure an email provider</span></span>

<span data-ttu-id="89539-122">このチュートリアルでは、 [Sendgrid](https://sendgrid.com) を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="89539-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="89539-123">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="89539-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="89539-124">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="89539-124">You can use other email providers.</span></span> <span data-ttu-id="89539-125">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89539-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="89539-126">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="89539-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="89539-127">SendGrid アカウントでは、 [送信者の追加](https://sendgrid.com/docs/ui/sending-email/senders/)が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89539-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="89539-128">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="89539-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="89539-129">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="89539-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="89539-130">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="89539-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="89539-131">とを `SendGridUser` 、 `SendGridKey` [シークレットマネージャーツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="89539-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="89539-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89539-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="89539-133">Windows では、シークレットマネージャーは、キーと値のペアをディレクトリ内のファイル \* のsecrets.js\* に格納 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` します。</span><span class="sxs-lookup"><span data-stu-id="89539-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="89539-134">ファイルの *secrets.js* の内容が暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="89539-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="89539-135">次のマークアップは、ファイル \* のsecrets.js\* を示しています。</span><span class="sxs-lookup"><span data-stu-id="89539-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="89539-136">`SendGridKey`値は削除されています。</span><span class="sxs-lookup"><span data-stu-id="89539-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="89539-137">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="89539-138">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="89539-138">Install SendGrid</span></span>

<span data-ttu-id="89539-139">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="89539-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="89539-140">NuGet パッケージをインストールし `SendGrid` ます。</span><span class="sxs-lookup"><span data-stu-id="89539-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89539-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89539-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89539-142">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="89539-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="89539-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89539-144">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="89539-145">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/) する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="89539-146">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="89539-146">Implement IEmailSender</span></span>

<span data-ttu-id="89539-147">を実装するには `IEmailSender` 、次のようなコードを使用して *サービス/emailsender* を作成します。</span><span class="sxs-lookup"><span data-stu-id="89539-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="89539-148">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="89539-148">Configure startup to support email</span></span>

<span data-ttu-id="89539-149">Startup.cs ファイルのメソッドに次のコードを追加し `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="89539-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="89539-150">`EmailSender`を一時サービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="89539-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="89539-151">`AuthMessageSenderOptions`構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="89539-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="89539-152">スキャフォールディング RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="89539-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="89539-153">[ Identity スキャフォールディング](xref:security/authentication/scaffold-identity)とスキャフォールディングの手順に従い `RegisterConfirmation` ます。</span><span class="sxs-lookup"><span data-stu-id="89539-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="89539-154">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="89539-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="89539-155">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="89539-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="89539-156">アプリを実行して新しいユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="89539-156">Run the app and register a new user</span></span>
* <span data-ttu-id="89539-157">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="89539-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="89539-158">電子メールを受信しない場合は、「 [デバッグ電子メール](#debug) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="89539-159">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="89539-160">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="89539-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="89539-161">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="89539-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="89539-162">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="89539-162">Test password reset</span></span>

* <span data-ttu-id="89539-163">サインインしている場合は、[ **ログアウト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="89539-164">[ **ログイン** ] リンクを選択し、[ **パスワードを忘れ** た場合] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="89539-165">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="89539-166">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="89539-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="89539-167">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="89539-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="89539-168">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="89539-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="89539-169">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="89539-169">Change email and activity timeout</span></span>

<span data-ttu-id="89539-170">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="89539-170">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="89539-171">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="89539-171">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="89539-172">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="89539-172">Change all data protection token lifespans</span></span>

<span data-ttu-id="89539-173">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="89539-173">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="89539-174">組み込みの Identity ユーザートークン (「 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には [1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="89539-174">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="89539-175">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="89539-175">Change the email token lifespan</span></span>

<span data-ttu-id="89539-176">[ Identity ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="89539-176">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="89539-177">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89539-177">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="89539-178">カスタム[ \<TUser> DataProtectorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)とを追加し <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="89539-178">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="89539-179">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="89539-179">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="89539-180">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="89539-180">Resend email confirmation</span></span>

<span data-ttu-id="89539-181">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-181">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="89539-182">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="89539-182">Debug email</span></span>

<span data-ttu-id="89539-183">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="89539-183">If you can't get email working:</span></span>

* <span data-ttu-id="89539-184">にブレークポイントを設定し `EmailSender.Execute` て、 `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="89539-185">同様のコードを使用して [電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) を作成 `EmailSender.Execute` します。</span><span class="sxs-lookup"><span data-stu-id="89539-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="89539-186">[ [電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html) ] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="89539-187">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-187">Check your spam folder.</span></span>
* <span data-ttu-id="89539-188">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="89539-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="89539-189">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="89539-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="89539-190">**セキュリティのベストプラクティス** として、テストと開発では運用シークレットを使用し **ないこと** をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89539-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="89539-191">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="89539-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="89539-192">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="89539-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="89539-193">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="89539-193">Combine social and local login accounts</span></span>

<span data-ttu-id="89539-194">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="89539-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="89539-195">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="89539-196">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="89539-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="89539-197">次のシーケンスでは、" RickAndMSFT@gmail.com " は最初にローカルログインとして作成されますが、最初にアカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="89539-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="89539-199">[ **管理** ] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="89539-199">Click on the **Manage** link.</span></span> <span data-ttu-id="89539-200">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="89539-200">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="89539-202">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="89539-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="89539-203">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="89539-203">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="89539-205">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="89539-205">The two accounts have been combined.</span></span> <span data-ttu-id="89539-206">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="89539-206">You are able to sign in with either account.</span></span> <span data-ttu-id="89539-207">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="89539-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="89539-208">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="89539-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="89539-209">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="89539-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="89539-210">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="89539-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="89539-211">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="89539-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="89539-212">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="89539-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="89539-213">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-213">Confirm existing users.</span></span> <span data-ttu-id="89539-214">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="89539-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="89539-215">前提条件</span><span class="sxs-lookup"><span data-stu-id="89539-215">Prerequisites</span></span>

[<span data-ttu-id="89539-216">.NET Core 2.2 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="89539-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="89539-217">Web アプリとスキャフォールディングを作成する Identity</span><span class="sxs-lookup"><span data-stu-id="89539-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="89539-218">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89539-218">Run the following commands to create a web app with authentication.</span></span>

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

> [!NOTE]
> <span data-ttu-id="89539-219">がで構成されている場合は <xref:Microsoft.AspNetCore.Identity.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` Identity ます。</span><span class="sxs-lookup"><span data-stu-id="89539-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="89539-220">`InputModel` `Password` プロパティは、 `Areas/Identity/Pages/Account/Register.cshtml.cs` スキャフォールディング後にファイルにあり Identity ます。</span><span class="sxs-lookup"><span data-stu-id="89539-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="89539-221">新しいユーザー登録をテストする</span><span class="sxs-lookup"><span data-stu-id="89539-221">Test new user registration</span></span>

<span data-ttu-id="89539-222">アプリを実行し、[ **登録** ] リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="89539-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="89539-223">この時点で、電子メールに対する検証は、属性を使用した場合のみです [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 。</span><span class="sxs-lookup"><span data-stu-id="89539-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="89539-224">登録を送信すると、アプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="89539-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="89539-225">このチュートリアルの後半では、電子メールが検証されるまで新しいユーザーがサインインできないように、コードが更新されています。</span><span class="sxs-lookup"><span data-stu-id="89539-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="89539-226">テーブルの `EmailConfirmed` フィールドがであることに注意 `False` してください。</span><span class="sxs-lookup"><span data-stu-id="89539-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="89539-227">アプリが確認の電子メールを送信するときに、次の手順でもう一度このメールを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="89539-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="89539-228">行を右クリックし、[ **削除**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="89539-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="89539-229">電子メールエイリアスを削除すると、次の手順が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="89539-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="89539-230">電子メールの確認が必要</span><span class="sxs-lookup"><span data-stu-id="89539-230">Require email confirmation</span></span>

<span data-ttu-id="89539-231">新しいユーザー登録の電子メールを確認することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89539-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="89539-232">電子メールの確認では、他のユーザーの権限を借用していないこと (つまり、他のユーザーの電子メールに登録されていないこと) を確認できます。</span><span class="sxs-lookup"><span data-stu-id="89539-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="89539-233">ディスカッションフォーラムを使用していて、"" を "" として登録できないようにしたいとし yli@example.com nolivetto@contoso.com ます。</span><span class="sxs-lookup"><span data-stu-id="89539-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="89539-234">電子メールを確認しない場合、" nolivetto@contoso.com " はアプリから不要な電子メールを受信する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89539-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="89539-235">ユーザーが誤って "" として登録され、 ylo@example.com "yli" のスペルミスに気付かないとします。</span><span class="sxs-lookup"><span data-stu-id="89539-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="89539-236">アプリには正しい電子メールがないため、パスワードの回復を使用できません。</span><span class="sxs-lookup"><span data-stu-id="89539-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="89539-237">電子メールの確認では、ボットからの保護が制限されています。</span><span class="sxs-lookup"><span data-stu-id="89539-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="89539-238">電子メールの確認では、多くの電子メールアカウントを持つ悪意のあるユーザーからの保護は提供されません。</span><span class="sxs-lookup"><span data-stu-id="89539-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="89539-239">通常は、確認済みの電子メールを送信する前に、新しいユーザーが web サイトにデータを投稿できないようにします。</span><span class="sxs-lookup"><span data-stu-id="89539-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="89539-240">`Startup.ConfigureServices`確認済みの電子メールを要求するように更新します。</span><span class="sxs-lookup"><span data-stu-id="89539-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="89539-241">`config.SignIn.RequireConfirmedEmail = true;` 登録されたユーザーが電子メールを確認するまでログインできないようにします。</span><span class="sxs-lookup"><span data-stu-id="89539-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="89539-242">電子メールプロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="89539-242">Configure email provider</span></span>

<span data-ttu-id="89539-243">このチュートリアルでは、 [Sendgrid](https://sendgrid.com) を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="89539-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="89539-244">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="89539-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="89539-245">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="89539-245">You can use other email providers.</span></span> <span data-ttu-id="89539-246">ASP.NET Core 2.x `System.Net.Mail` には、アプリから電子メールを送信できるようにするが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89539-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="89539-247">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89539-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="89539-248">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="89539-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="89539-249">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="89539-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="89539-250">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="89539-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="89539-251">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="89539-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="89539-252">とを `SendGridUser` 、 `SendGridKey` [シークレットマネージャーツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="89539-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="89539-253">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89539-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="89539-254">Windows では、シークレットマネージャーは、キーと値のペアをディレクトリ内のファイル \* のsecrets.js\* に格納 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` します。</span><span class="sxs-lookup"><span data-stu-id="89539-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="89539-255">ファイルの *secrets.js* の内容が暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="89539-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="89539-256">次のマークアップは、ファイル \* のsecrets.js\* を示しています。</span><span class="sxs-lookup"><span data-stu-id="89539-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="89539-257">`SendGridKey`値は削除されています。</span><span class="sxs-lookup"><span data-stu-id="89539-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="89539-258">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="89539-259">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="89539-259">Install SendGrid</span></span>

<span data-ttu-id="89539-260">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="89539-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="89539-261">NuGet パッケージをインストールし `SendGrid` ます。</span><span class="sxs-lookup"><span data-stu-id="89539-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89539-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89539-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89539-263">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="89539-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="89539-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89539-265">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="89539-266">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/) する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="89539-267">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="89539-267">Implement IEmailSender</span></span>

<span data-ttu-id="89539-268">を実装するには `IEmailSender` 、次のようなコードを使用して *サービス/emailsender* を作成します。</span><span class="sxs-lookup"><span data-stu-id="89539-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="89539-269">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="89539-269">Configure startup to support email</span></span>

<span data-ttu-id="89539-270">Startup.cs ファイルのメソッドに次のコードを追加し `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="89539-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="89539-271">`EmailSender`を一時サービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="89539-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="89539-272">`AuthMessageSenderOptions`構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="89539-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="89539-273">アカウントの確認とパスワードの回復を有効にする</span><span class="sxs-lookup"><span data-stu-id="89539-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="89539-274">このテンプレートには、アカウントの確認とパスワードの回復のためのコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89539-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="89539-275">`OnPostAsync` *Areas/ Identity /Pages/Account/Register.cshtml.cs*でメソッドを検索します。</span><span class="sxs-lookup"><span data-stu-id="89539-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="89539-276">新しく登録されたユーザーが自動的にサインインしないようにするには、次の行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="89539-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="89539-277">完全なメソッドは、変更された行が強調表示された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="89539-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="89539-278">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="89539-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="89539-279">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="89539-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="89539-280">アプリを実行して新しいユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="89539-280">Run the app and register a new user</span></span>
* <span data-ttu-id="89539-281">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="89539-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="89539-282">電子メールを受信しない場合は、「 [デバッグ電子メール](#debug) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="89539-283">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="89539-284">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="89539-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="89539-285">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="89539-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="89539-286">[管理] ページを表示する</span><span class="sxs-lookup"><span data-stu-id="89539-286">View the manage page</span></span>

<span data-ttu-id="89539-287">ブラウザーでユーザー名を選択します: ![ ユーザー名が表示されたブラウザーウィンドウ](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="89539-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="89539-288">[管理] ページが、[ **プロファイル** ] タブが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="89539-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="89539-289">電子メールには、電子メールが確認されたことを示すチェックボックス **が表示さ** れます。</span><span class="sxs-lookup"><span data-stu-id="89539-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="89539-290">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="89539-290">Test password reset</span></span>

* <span data-ttu-id="89539-291">サインインしている場合は、[ **ログアウト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="89539-292">[ **ログイン** ] リンクを選択し、[ **パスワードを忘れ** た場合] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="89539-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="89539-293">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="89539-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="89539-294">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="89539-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="89539-295">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="89539-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="89539-296">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="89539-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="89539-297">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="89539-297">Change email and activity timeout</span></span>

<span data-ttu-id="89539-298">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="89539-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="89539-299">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="89539-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="89539-300">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="89539-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="89539-301">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="89539-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="89539-302">組み込みの Identity ユーザートークン (「 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には [1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="89539-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="89539-303">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="89539-303">Change the email token lifespan</span></span>

<span data-ttu-id="89539-304">[ Identity ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="89539-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="89539-305">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89539-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="89539-306">カスタム[ \<TUser> DataProtectorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)とを追加し <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="89539-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="89539-307">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="89539-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="89539-308">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="89539-308">Resend email confirmation</span></span>

<span data-ttu-id="89539-309">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="89539-310">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="89539-310">Debug email</span></span>

<span data-ttu-id="89539-311">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="89539-311">If you can't get email working:</span></span>

* <span data-ttu-id="89539-312">にブレークポイントを設定し `EmailSender.Execute` て、 `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="89539-313">同様のコードを使用して [電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) を作成 `EmailSender.Execute` します。</span><span class="sxs-lookup"><span data-stu-id="89539-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="89539-314">[ [電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html) ] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="89539-315">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-315">Check your spam folder.</span></span>
* <span data-ttu-id="89539-316">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="89539-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="89539-317">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="89539-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="89539-318">**セキュリティのベストプラクティス** として、テストと開発では運用シークレットを使用し **ないこと** をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89539-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="89539-319">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定できます。</span><span class="sxs-lookup"><span data-stu-id="89539-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="89539-320">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="89539-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="89539-321">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="89539-321">Combine social and local login accounts</span></span>

<span data-ttu-id="89539-322">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="89539-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="89539-323">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89539-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="89539-324">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="89539-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="89539-325">次のシーケンスでは、" RickAndMSFT@gmail.com " は最初にローカルログインとして作成されますが、最初にアカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="89539-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="89539-327">[ **管理** ] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="89539-327">Click on the **Manage** link.</span></span> <span data-ttu-id="89539-328">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="89539-328">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="89539-330">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="89539-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="89539-331">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="89539-331">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="89539-333">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="89539-333">The two accounts have been combined.</span></span> <span data-ttu-id="89539-334">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="89539-334">You are able to sign in with either account.</span></span> <span data-ttu-id="89539-335">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="89539-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="89539-336">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="89539-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="89539-337">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="89539-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="89539-338">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="89539-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="89539-339">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="89539-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="89539-340">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="89539-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="89539-341">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="89539-341">Confirm existing users.</span></span> <span data-ttu-id="89539-342">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="89539-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
