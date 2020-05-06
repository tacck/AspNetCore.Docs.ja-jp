---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775792"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="601e8-103">ASP.NET Core を使用した Twitter の外部サインインセットアップ</span><span class="sxs-lookup"><span data-stu-id="601e8-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="601e8-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="601e8-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="601e8-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="601e8-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="601e8-106">Twitter でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="601e8-106">Create the app in Twitter</span></span>

* <span data-ttu-id="601e8-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="601e8-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="601e8-108">に[https://apps.twitter.com/](https://apps.twitter.com/)移動してサインインします。</span><span class="sxs-lookup"><span data-stu-id="601e8-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="601e8-109">まだ Twitter アカウントをお持ちでない場合は、[**[今すぐサインアップ](https://twitter.com/signup)**] リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="601e8-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="601e8-110">[**アプリの作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="601e8-110">Select **Create an app**.</span></span> <span data-ttu-id="601e8-111">**アプリ名**、**アプリケーションの説明**、およびパブリック**web サイト**の URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。</span><span class="sxs-lookup"><span data-stu-id="601e8-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="601e8-112">[ **Twitter でのサインインを有効**にする] の横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="601e8-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="601e8-113">AspNetCore。Identity</span><span class="sxs-lookup"><span data-stu-id="601e8-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="601e8-114">既定では、ユーザーは電子メールアドレスを持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="601e8-114">requires users to have an email address by default.</span></span> <span data-ttu-id="601e8-115">[**アクセス許可**] タブにアクセスし、[**編集**] ボタンをクリックして、[**ユーザーに電子メールアドレスを要求**する] の横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="601e8-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="601e8-116">[**コールバック url** ] `/signin-twitter`フィールドに追加された開発 URI を入力`https://webapp128.azurewebsites.net/signin-twitter`します (例:)。</span><span class="sxs-lookup"><span data-stu-id="601e8-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="601e8-117">このサンプルで後で構成される Twitter 認証スキームは、OAuth `/signin-twitter`フローを実装するために、ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="601e8-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="601e8-118">URI セグメント`/signin-twitter`は、Twitter 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="601e8-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="601e8-119">[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="601e8-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="601e8-120">フォームの残りの部分を入力し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="601e8-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="601e8-121">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="601e8-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="601e8-122">Twitter コンシューマー API キーとシークレットを格納する</span><span class="sxs-lookup"><span data-stu-id="601e8-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="601e8-123">Twitter コンシューマー API キーや secret [Manager](xref:security/app-secrets)とシークレットなどの機微な設定を格納します。</span><span class="sxs-lookup"><span data-stu-id="601e8-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="601e8-124">このサンプルでは、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="601e8-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="601e8-125">「[シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="601e8-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="601e8-126">秘密キーとシークレットキー `Authentication:Twitter:ConsumerKey`を使用して、機密設定をローカル`Authentication:Twitter:ConsumerSecret`シークレットストアに保存します。</span><span class="sxs-lookup"><span data-stu-id="601e8-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="601e8-127">これらのトークンは、新しい Twitter アプリケーションを作成した後の [**キーとアクセストークン**] タブにあります。</span><span class="sxs-lookup"><span data-stu-id="601e8-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="601e8-128">Twitter 認証の構成</span><span class="sxs-lookup"><span data-stu-id="601e8-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="601e8-129">`ConfigureServices` *Startup.cs*ファイルのメソッドに Twitter サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="601e8-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="601e8-130">Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="601e8-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="601e8-131">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="601e8-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="601e8-132">Twitter でのサインイン</span><span class="sxs-lookup"><span data-stu-id="601e8-132">Sign in with Twitter</span></span>

<span data-ttu-id="601e8-133">アプリを実行し、[**ログイン**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="601e8-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="601e8-134">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="601e8-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="601e8-135">**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。</span><span class="sxs-lookup"><span data-stu-id="601e8-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="601e8-136">Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="601e8-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="601e8-137">これで、Twitter の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="601e8-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="601e8-138">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="601e8-138">Troubleshooting</span></span>

* <span data-ttu-id="601e8-139">**ASP.NET Core 2.x のみ:** でIdentity `ConfigureServices`を呼び出す`services.AddIdentity`ことによって構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="601e8-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="601e8-140">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="601e8-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="601e8-141">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="601e8-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="601e8-142">[**移行の適用**] をタップしてデータベースを作成し、更新してエラーを続行します。</span><span class="sxs-lookup"><span data-stu-id="601e8-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="601e8-143">次のステップ</span><span class="sxs-lookup"><span data-stu-id="601e8-143">Next steps</span></span>

* <span data-ttu-id="601e8-144">この記事では、Twitter で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="601e8-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="601e8-145">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="601e8-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="601e8-146">Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータル`ConsumerSecret`でをリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="601e8-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="601e8-147">`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`を、Azure portal のアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="601e8-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="601e8-148">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="601e8-148">The configuration system is set up to read keys from environment variables.</span></span>
