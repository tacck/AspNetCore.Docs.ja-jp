---
title: ASP.NETコアを使用したTwitter外部サインイン設定
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277289"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="b15bd-103">ASP.NETコアを使用したTwitter外部サインイン設定</span><span class="sxs-lookup"><span data-stu-id="b15bd-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="b15bd-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b15bd-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b15bd-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[自分の Twitter アカウントでサインイン](https://dev.twitter.com/web/sign-in/desktop-browser)できるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="b15bd-106">ツイッターでアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b15bd-106">Create the app in Twitter</span></span>

* <span data-ttu-id="b15bd-107">プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0)追加します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="b15bd-108">に[https://apps.twitter.com/](https://apps.twitter.com/)移動し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="b15bd-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="b15bd-109">Twitter アカウントをまだお持ちの場合は、[**[今すぐサインアップ](https://twitter.com/signup)**] リンクを使用してアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="b15bd-110">[**アプリの作成] を**選択します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-110">Select **Create an app**.</span></span> <span data-ttu-id="b15bd-111">**アプリ名**、**アプリケーションの説明**、およびパブリック**Web サイト**URI を入力します (ドメイン名を登録するまでは一時的に行うことができます)。</span><span class="sxs-lookup"><span data-stu-id="b15bd-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="b15bd-112">[Twitterでサインインを**有効にする] の横にあるチェックボックスをオンにします。**</span><span class="sxs-lookup"><span data-stu-id="b15bd-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="b15bd-113">既定では、ユーザーに電子メール アドレスを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b15bd-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="b15bd-114">[**アクセス権**] タブに移動し、[**編集**] ボタンをクリックして、[**ユーザーからの電子メール アドレスの要求]** の横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="b15bd-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="b15bd-115">**[コールバック URL]** フィールドに`/signin-twitter`追加された開発 URI を入力します`https://webapp128.azurewebsites.net/signin-twitter`(例: )。</span><span class="sxs-lookup"><span data-stu-id="b15bd-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="b15bd-116">このサンプルで後述する Twitter 認証スキームは、OAuth フローを実装するために、ルートで`/signin-twitter`要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b15bd-117">URI セグメント`/signin-twitter`は、Twitter 認証プロバイダーのデフォルトのコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="b15bd-118">既定のコールバック URI は、継承された[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)クラスのプロパティを使用して、Twitter 認証ミドル[ウェアを構成](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)するときに変更できます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="b15bd-119">フォームの残りの部分に入力し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="b15bd-120">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="b15bd-121">Twitter コンシューマー API キーとシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="b15bd-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="b15bd-122">秘密マネージャーで、Twitter コンシューマー API キーやシークレットなどの機密性の高い設定を保存[します](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="b15bd-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b15bd-123">このサンプルでは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="b15bd-124">「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="b15bd-125">秘密キーとローカルシークレットストアに機密設定を保存`Authentication:Twitter:ConsumerKey`し、 `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="b15bd-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b15bd-126">これらのトークンは、新しいTwitterアプリケーションを作成した後に **[キーとアクセストークン**]タブで見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="b15bd-127">ツイッター認証の設定</span><span class="sxs-lookup"><span data-stu-id="b15bd-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="b15bd-128">ファイル内の`ConfigureServices`メソッドにTwitterサービス*Startup.cs*追加します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="b15bd-129">Twitter 認証でサポートされる設定オプションの詳細については[、TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b15bd-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="b15bd-130">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="b15bd-131">ツイッターでサインインする</span><span class="sxs-lookup"><span data-stu-id="b15bd-131">Sign in with Twitter</span></span>

<span data-ttu-id="b15bd-132">アプリを実行し、[**ログイン**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="b15bd-133">Twitterでログインするオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="b15bd-134">**Twitter**をクリックすると、認証のためにTwitterにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="b15bd-135">Twitterの認証情報を入力すると、メールを設定できる Web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="b15bd-136">これで、Twitter の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="b15bd-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="b15bd-137">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b15bd-137">Troubleshooting</span></span>

* <span data-ttu-id="b15bd-138">**ASP.NETコア 2.x のみ:** で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。</span><span class="sxs-lookup"><span data-stu-id="b15bd-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="b15bd-139">このサンプルで使用されているプロジェクト テンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="b15bd-140">初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。</span><span class="sxs-lookup"><span data-stu-id="b15bd-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b15bd-141">[**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b15bd-142">次のステップ</span><span class="sxs-lookup"><span data-stu-id="b15bd-142">Next steps</span></span>

* <span data-ttu-id="b15bd-143">この記事では、Twitterで認証する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="b15bd-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="b15bd-144">同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b15bd-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="b15bd-145">Web サイトを Azure Web アプリに発行したら、Twitter`ConsumerSecret`開発者ポータル で をリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b15bd-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="b15bd-146">と`Authentication:Twitter:ConsumerKey`として`Authentication:Twitter:ConsumerSecret`アプリケーションの設定を Azure ポータルで設定します。</span><span class="sxs-lookup"><span data-stu-id="b15bd-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="b15bd-147">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="b15bd-147">The configuration system is set up to read keys from environment variables.</span></span>
