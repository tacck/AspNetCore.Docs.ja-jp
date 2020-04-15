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
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="ce2f9-103">ASP.NET コアを使用した Microsoft アカウント外部ログインセットアップ</span><span class="sxs-lookup"><span data-stu-id="ce2f9-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="ce2f9-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ce2f9-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ce2f9-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが職場、学校、または個人の Microsoft アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="ce2f9-106">Microsoft 開発者ポータルでアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="ce2f9-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="ce2f9-107">プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/)追加します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="ce2f9-108">[Azure ポータル - アプリ登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="ce2f9-109">Microsoft アカウントをお持ちの場合は、[**アカウントを作成**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="ce2f9-110">サインイン後、**アプリの登録**ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="ce2f9-111">**新規登録の**選択</span><span class="sxs-lookup"><span data-stu-id="ce2f9-111">Select **New registration**</span></span>
* <span data-ttu-id="ce2f9-112">**[名前]** を入力します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-112">Enter a **Name**.</span></span>
* <span data-ttu-id="ce2f9-113">[**サポートされているアカウントの種類]** のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="ce2f9-114">[**リダイレクト URI]** に、開発`/signin-microsoft`用 URL を追加して入力します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="ce2f9-115">たとえば、「 `https://localhost:5001/signin-microsoft` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="ce2f9-116">このサンプルで後述する Microsoft 認証スキームは、OAuth フローを実装するルートで`/signin-microsoft`要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="ce2f9-117">**[登録]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ce2f9-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="ce2f9-118">クライアント シークレットを作成する</span><span class="sxs-lookup"><span data-stu-id="ce2f9-118">Create client secret</span></span>

* <span data-ttu-id="ce2f9-119">左側のウィンドウで、**[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="ce2f9-120">[**クライアント シークレット**] で 、[**新しいクライアント シークレット**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="ce2f9-121">クライアント シークレットの説明を追加します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="ce2f9-122">[**追加**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-122">Select the **Add** button.</span></span>

* <span data-ttu-id="ce2f9-123">[**クライアント シークレット**] の下で、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="ce2f9-124">URI セグメント`/signin-microsoft`は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="ce2f9-125">クラスの継承された[リモート認証オプション.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用してマイクロソフト認証ミドル[ウェアを構成](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)する際に、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="ce2f9-126">マイクロソフトのクライアント ID とシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="ce2f9-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="ce2f9-127">秘密マネージャー を使用して、Microsoft クライアント ID やシークレット値などの機密性の高い設定を保存[します](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="ce2f9-128">このサンプルでは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="ce2f9-129">「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="ce2f9-130">秘密鍵とローカルシークレットストアに機密設定を保存`Authentication:Microsoft:ClientId`し、 `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="ce2f9-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="ce2f9-131">マイクロソフト アカウント認証の構成</span><span class="sxs-lookup"><span data-stu-id="ce2f9-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="ce2f9-132">に Microsoft アカウント サービス`Startup.ConfigureServices`を追加します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="ce2f9-133">Microsoft アカウント認証でサポートされる構成オプションの詳細については[、API](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="ce2f9-134">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="ce2f9-135">マイクロソフト アカウントでサインインする</span><span class="sxs-lookup"><span data-stu-id="ce2f9-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="ce2f9-136">アプリを実行し、[**ログイン**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="ce2f9-137">マイクロソフトでサインインするオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="ce2f9-138">マイクロソフトをクリックすると、認証のためにマイクロソフトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="ce2f9-139">Microsoft アカウントでサインインすると、アプリが情報にアクセスするように求められます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="ce2f9-140">[**はい**]をタップすると、メールを設定できるWebサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="ce2f9-141">これで、Microsoft の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="ce2f9-142">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ce2f9-142">Troubleshooting</span></span>

* <span data-ttu-id="ce2f9-143">Microsoft アカウント プロバイダーがサインイン エラー ページにリダイレクトする場合は、URI の (ハッシュタグ) の直後`#`にエラー のタイトルと説明のクエリ文字列パラメーターをメモします。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="ce2f9-144">エラー メッセージは Microsoft 認証の問題を示しているように見えますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定された**リダイレクト URI のいずれ**にも一致しないことです。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="ce2f9-145">で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="ce2f9-146">このサンプルで使用されているプロジェクト テンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="ce2f9-147">初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="ce2f9-148">[**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ce2f9-149">次のステップ</span><span class="sxs-lookup"><span data-stu-id="ce2f9-149">Next steps</span></span>

* <span data-ttu-id="ce2f9-150">この記事では、マイクロソフトで認証を行う方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="ce2f9-151">同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="ce2f9-152">Web サイトを Azure Web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアント シークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="ce2f9-153">と`Authentication:Microsoft:ClientId`として`Authentication:Microsoft:ClientSecret`アプリケーションの設定を Azure ポータルで設定します。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="ce2f9-154">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="ce2f9-154">The configuration system is set up to read keys from environment variables.</span></span>
