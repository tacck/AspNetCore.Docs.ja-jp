---
title: ASP.NET Core での SMS による2要素認証
author: rick-anderson
description: ASP.NET Core アプリで2要素認証 (2FA) を設定する方法について説明します。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: e5e606afaf0219f3a0eb7301203b7142a00322be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634112"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="89054-103">ASP.NET Core での SMS による2要素認証</span><span class="sxs-lookup"><span data-stu-id="89054-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="89054-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) および [スイス-開発者](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="89054-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="89054-105">2要素認証 (2FA) 認証アプリは、時間ベースのワンタイムパスワードアルゴリズム (TOTP) を使用して、2FA に業界で推奨されるアプローチです。</span><span class="sxs-lookup"><span data-stu-id="89054-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="89054-106">TOTP を使用した2FA は、SMS 2FA に適しています。</span><span class="sxs-lookup"><span data-stu-id="89054-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="89054-107">詳細については、ASP.NET Core 2.0 以降の [ASP.NET Core での TOTP authenticator アプリの QR コード生成の有効化](xref:security/authentication/identity-enable-qrcodes) に関する説明を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89054-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="89054-108">このチュートリアルでは、SMS を使用して2要素認証 (2FA) を設定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="89054-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="89054-109">手順については、 [twilio](https://www.twilio.com/) と [aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)を参照してください。ただし、他の sms プロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="89054-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="89054-110">このチュートリアルを開始する前に [、アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm) を完了することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89054-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="89054-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)します。</span><span class="sxs-lookup"><span data-stu-id="89054-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="89054-112">[ダウンロード方法](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="89054-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="89054-113">新しい ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="89054-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="89054-114">`Web2FA`個々のユーザーアカウントを使用して、という名前の新しい ASP.NET Core web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="89054-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="89054-115">「」の手順に従っ <xref:security/enforcing-ssl> て、HTTPS を設定し、要求します。</span><span class="sxs-lookup"><span data-stu-id="89054-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="89054-116">SMS アカウントの作成</span><span class="sxs-lookup"><span data-stu-id="89054-116">Create an SMS account</span></span>

<span data-ttu-id="89054-117">たとえば、 [twilio](https://www.twilio.com/) や [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)などの sms アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="89054-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="89054-118">認証資格情報 (ASPSMS の場合は twilio: accountSid および authToken の場合はユーザーキーとパスワード) を記録します。</span><span class="sxs-lookup"><span data-stu-id="89054-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="89054-119">SMS プロバイダーの資格情報の確認</span><span class="sxs-lookup"><span data-stu-id="89054-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="89054-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="89054-120">**Twilio:**</span></span>

<span data-ttu-id="89054-121">Twilio アカウントの [ダッシュボード] タブで、 **アカウント SID** と **認証トークン**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="89054-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="89054-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="89054-122">**ASPSMS:**</span></span>

<span data-ttu-id="89054-123">アカウントの設定から **ユーザーキー** に移動し、 **パスワード**と共にコピーします。</span><span class="sxs-lookup"><span data-stu-id="89054-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="89054-124">これらの値は、後で、キーと内の secret manager ツールを使用してに格納し `SMSAccountIdentification` `SMSAccountPassword` ます。</span><span class="sxs-lookup"><span data-stu-id="89054-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="89054-125">SenderID/オリジネータの指定</span><span class="sxs-lookup"><span data-stu-id="89054-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="89054-126">**Twilio:** [数値] タブで、Twilio **電話番号**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="89054-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="89054-127">**Aspsms:** [発信元のロック解除] メニュー内で、1つ以上の発信者をロック解除するか、英数字の発信者を選択します (すべてのネットワークでサポートされていません)。</span><span class="sxs-lookup"><span data-stu-id="89054-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="89054-128">この値は、後でキー内にシークレットマネージャーツールで格納し `SMSAccountFrom` ます。</span><span class="sxs-lookup"><span data-stu-id="89054-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="89054-129">SMS サービスの資格情報を指定する</span><span class="sxs-lookup"><span data-stu-id="89054-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="89054-130">[オプションパターン](xref:fundamentals/configuration/options)を使用して、ユーザーアカウントとキーの設定にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="89054-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="89054-131">セキュリティで保護された SMS キーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="89054-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="89054-132">このサンプルでは、 `SMSoptions` *サービス/smsoptions .cs* ファイルにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="89054-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="89054-133">、、 `SMSAccountIdentification` `SMSAccountPassword` およびを `SMSAccountFrom` [secret manager ツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="89054-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="89054-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89054-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="89054-135">SMS プロバイダーの NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="89054-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="89054-136">パッケージマネージャーコンソール (PMC) から次のように実行します。</span><span class="sxs-lookup"><span data-stu-id="89054-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="89054-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="89054-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="89054-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="89054-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="89054-139">*サービス/MessageServices .cs*ファイルにコードを追加して、SMS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="89054-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="89054-140">Twilio または ASPSMS セクションのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="89054-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="89054-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="89054-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="89054-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="89054-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="89054-143">使用するスタートアップの構成 `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="89054-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="89054-144">`SMSoptions`Startup.cs のメソッドで、サービスコンテナーにを追加し `ConfigureServices` ます。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="89054-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="89054-145">2 要素認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="89054-145">Enable two-factor authentication</span></span>

<span data-ttu-id="89054-146">*Views/Manage/Index. cshtml* Razor ビューファイルを開き、コメント文字を削除します (マークアップがコメントアウトされることはありません)。</span><span class="sxs-lookup"><span data-stu-id="89054-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="89054-147">2要素認証を使用してログインする</span><span class="sxs-lookup"><span data-stu-id="89054-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="89054-148">アプリを実行して新しいユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="89054-148">Run the app and register a new user</span></span>

![Microsoft Edge で開いている Web アプリケーション登録ビュー](2fa/_static/login2fa1.png)

* <span data-ttu-id="89054-150">[コントローラーの管理] でアクションメソッドをアクティブにするユーザー名をタップし `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="89054-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="89054-151">次に、[電話番号の **追加** ] リンクをタップします。</span><span class="sxs-lookup"><span data-stu-id="89054-151">Then tap the phone number **Add** link.</span></span>

![[管理] の [追加] リンク](2fa/_static/login2fa2.png)

* <span data-ttu-id="89054-153">確認コードを受信する電話番号を追加し、[ **確認コードの送信**] をタップします。</span><span class="sxs-lookup"><span data-stu-id="89054-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![[電話番号の追加] ページ](2fa/_static/login2fa3.png)

* <span data-ttu-id="89054-155">確認コードを含むテキストメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89054-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="89054-156">入力し、[**送信**] をタップします。</span><span class="sxs-lookup"><span data-stu-id="89054-156">Enter it and tap **Submit**</span></span>

![[電話番号の確認] ページ](2fa/_static/login2fa4.png)

<span data-ttu-id="89054-158">テキストメッセージが表示されない場合は、「twilio log page」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89054-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="89054-159">管理ビューには、電話番号が正常に追加されたことが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89054-159">The Manage view shows your phone number was added successfully.</span></span>

![ビューの管理-電話番号が正常に追加されました](2fa/_static/login2fa5.png)

* <span data-ttu-id="89054-161">[ **有効** ] をタップして、2要素認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="89054-161">Tap **Enable** to enable two-factor authentication.</span></span>

![ビューの管理-2 要素認証の有効化](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="89054-163">2要素認証をテストする</span><span class="sxs-lookup"><span data-stu-id="89054-163">Test two-factor authentication</span></span>

* <span data-ttu-id="89054-164">ログオフします。</span><span class="sxs-lookup"><span data-stu-id="89054-164">Log off.</span></span>

* <span data-ttu-id="89054-165">ログインします。</span><span class="sxs-lookup"><span data-stu-id="89054-165">Log in.</span></span>

* <span data-ttu-id="89054-166">ユーザーアカウントで2要素認証が有効になっているため、2番目の認証要素を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="89054-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="89054-167">このチュートリアルでは、電話による検証を有効にしました。</span><span class="sxs-lookup"><span data-stu-id="89054-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="89054-168">組み込みのテンプレートでは、2番目の要素として電子メールを設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="89054-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="89054-169">QR コードなどの認証については、さらに2つ目の要素を設定できます。</span><span class="sxs-lookup"><span data-stu-id="89054-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="89054-170">[ **Submit (送信**)」をタップします。</span><span class="sxs-lookup"><span data-stu-id="89054-170">Tap **Submit**.</span></span>

![確認コードビューの送信](2fa/_static/login2fa7.png)

* <span data-ttu-id="89054-172">SMS メッセージで取得したコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="89054-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="89054-173">[ **このブラウザーを記憶** する] チェックボックスをオンにすると、同じデバイスとブラウザーを使用しているときに2fa を使用してログオンする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="89054-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="89054-174">2FA を有効にして [ **このブラウザーを記憶** する] をオンにすると、悪意のあるユーザーが自分のデバイスにアクセスできなくても、アカウントへのアクセスを試みる悪意のあるユーザーからの強力な2fa 保護が提供されます。</span><span class="sxs-lookup"><span data-stu-id="89054-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="89054-175">この操作は、定期的に使用するプライベートデバイスで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="89054-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="89054-176">[  **このブラウザーを記憶**する] を設定すると、定期的に使用しないデバイスから2fa のセキュリティが強化され、自分のデバイスで2fa を通過する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="89054-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![ビューの確認](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="89054-178">ブルートフォース攻撃から保護するためのアカウントロックアウト</span><span class="sxs-lookup"><span data-stu-id="89054-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="89054-179">アカウントのロックアウトは2FA で推奨されます。</span><span class="sxs-lookup"><span data-stu-id="89054-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="89054-180">ユーザーがローカルアカウントまたはソーシャルアカウントを使用してサインインすると、2FA に失敗した各操作が格納されます。</span><span class="sxs-lookup"><span data-stu-id="89054-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="89054-181">失敗したアクセス試行の最大回数に達した場合、ユーザーはロックアウトされます (既定では5分間のロックアウトが失敗し、アクセス試行が5回失敗します)。</span><span class="sxs-lookup"><span data-stu-id="89054-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="89054-182">認証が成功すると、失敗したアクセス試行回数がリセットされ、時計がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="89054-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="89054-183">失敗したアクセス試行の最大回数とロックアウト時間は、 [Maxfailed Accessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) と [Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)で設定できます。</span><span class="sxs-lookup"><span data-stu-id="89054-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="89054-184">次の例では、10分間に失敗したアクセス試行の10分後にアカウントのロックアウトを構成します。</span><span class="sxs-lookup"><span data-stu-id="89054-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="89054-185">[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)がに設定されていること `lockoutOnFailure` を確認し `true` ます。</span><span class="sxs-lookup"><span data-stu-id="89054-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
