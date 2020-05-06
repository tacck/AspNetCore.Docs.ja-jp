---
title: ASP.NET Core での Facebook 外部ログインセットアップ
author: rick-anderson
description: Facebook アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を示すコード例を紹介したチュートリアルです。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777034"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="bd4d5-103">ASP.NET Core での Facebook 外部ログインセットアップ</span><span class="sxs-lookup"><span data-stu-id="bd4d5-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="bd4d5-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd4d5-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="bd4d5-105">このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Facebook アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="bd4d5-106">まず、[正式な手順](https://developers.facebook.com)に従って FACEBOOK アプリ ID を作成します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="bd4d5-107">Facebook でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="bd4d5-107">Create the app in Facebook</span></span>

* <span data-ttu-id="bd4d5-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="bd4d5-109">[Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="bd4d5-110">まだ Facebook アカウントを持っていない場合は、ログインページの [ **facebook へのサインアップ**] リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="bd4d5-111">Facebook アカウントを作成したら、手順に従って Facebook 開発者として登録します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="bd4d5-112">**[マイアプリ**] メニューで、[**アプリの作成**] を選択して新しいアプリ ID を作成します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![開発者向け Facebook ポータルを Microsoft Edge で開く](index/_static/FBMyApps.png)

* <span data-ttu-id="bd4d5-114">フォームに入力し、[**アプリ ID の作成**] ボタンをタップします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![新しいアプリ ID フォームを作成する](index/_static/FBNewAppId.png)

* <span data-ttu-id="bd4d5-116">新しいアプリカードで、[**製品の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="bd4d5-117">**Facebook ログイン**カードで、 **[セットアップ] をクリックし**ます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![製品のセットアップページ](index/_static/FBProductSetup.png)

* <span data-ttu-id="bd4d5-119">**クイックスタート**ウィザードが起動し、最初のページとして **[プラットフォーム] を選択**します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="bd4d5-120">ここでウィザードをバイパスするには、左下のメニューで [ **FaceBook のログイン\*\*\*\*設定**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![スキップクイックスタート](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="bd4d5-122">[**クライアントの OAuth 設定**] ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="bd4d5-124">[**有効な OAuth リダイレクト uri** ] フィールドに */signin-facebook*を追加して、開発 URI を`https://localhost:44320/signin-facebook`入力します (例:)。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="bd4d5-125">このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook* route で要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="bd4d5-126">URI */signin-facebook*は、facebook 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="bd4d5-127">[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Facebook 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="bd4d5-128">**[変更を保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="bd4d5-129">左側のナビゲーションで、[**設定** > ] [**基本**リンク] の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="bd4d5-130">このページで、 `App ID`とをメモしておき`App Secret`ます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="bd4d5-131">次のセクションでは、両方を ASP.NET Core アプリケーションに追加します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="bd4d5-132">サイトをデプロイするときに、 **Facebook ログイン**のセットアップページを再表示し、新しいパブリック URI を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="bd4d5-133">Facebook アプリ ID とシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="bd4d5-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="bd4d5-134">Facebook アプリ ID やシークレット値などの機微な設定を[Secret Manager](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="bd4d5-135">このサンプルでは、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="bd4d5-136">「[シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="bd4d5-137">秘密キーとシークレットキー `Authentication:Facebook:AppId`を使用して、機密設定をローカル`Authentication:Facebook:AppSecret`シークレットストアに保存します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="bd4d5-138">Facebook 認証の構成</span><span class="sxs-lookup"><span data-stu-id="bd4d5-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="bd4d5-139">`ConfigureServices` *Startup.cs*ファイルのメソッドに Facebook サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="bd4d5-140">Facebook でサインインする</span><span class="sxs-lookup"><span data-stu-id="bd4d5-140">Sign in with Facebook</span></span>

* <span data-ttu-id="bd4d5-141">アプリを実行し、[**ログイン**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="bd4d5-142">[**別のサービスを使用してログインする**] で、[Facebook] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="bd4d5-143">認証のために**Facebook**にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="bd4d5-144">Facebook の資格情報を入力します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="bd4d5-145">メールを設定できるサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="bd4d5-146">Facebook の資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="bd4d5-147">外部サインインの承認の取り消しに対処する</span><span class="sxs-lookup"><span data-stu-id="bd4d5-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="bd4d5-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>ユーザーが要求された承認要求を承認しない場合、ユーザーエージェントへのリダイレクトパスを提供できます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="bd4d5-149">次のコードでは`AccessDeniedPath` 、 `"/AccessDeniedPathInfo"`をに設定しています。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="bd4d5-150">ページには`AccessDeniedPath`次の情報が含まれていることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="bd4d5-151">リモート認証が取り消されました。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="bd4d5-152">このアプリには認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-152">This app requires authentication.</span></span>
* <span data-ttu-id="bd4d5-153">もう一度サインインを試すには、[ログイン] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="bd4d5-154">テスト AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="bd4d5-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="bd4d5-155">[Facebook.com](https://www.facebook.com/)に移動します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="bd4d5-156">サインインしている場合は、サインアウトする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="bd4d5-157">アプリを実行し、[Facebook サインイン] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="bd4d5-158">[**今後**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-158">Select **Not now**.</span></span> <span data-ttu-id="bd4d5-159">指定さ`AccessDeniedPath`れたページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="bd4d5-160">Facebook 認証でサポートされる構成オプションの詳細については、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="bd4d5-161">構成オプションは、次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="bd4d5-162">ユーザーに関するさまざまな情報を要求します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-162">Request different information about the user.</span></span>
* <span data-ttu-id="bd4d5-163">クエリ文字列引数を追加して、ログインエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bd4d5-164">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bd4d5-164">Troubleshooting</span></span>

* <span data-ttu-id="bd4d5-165">**ASP.NET Core 2.x のみ:** でIdentity `ConfigureServices`を呼び出す`services.AddIdentity`ことによって構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="bd4d5-166">このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="bd4d5-167">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="bd4d5-168">[**移行の適用**] をタップしてデータベースを作成し、更新してエラーを続行します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bd4d5-169">次のステップ</span><span class="sxs-lookup"><span data-stu-id="bd4d5-169">Next steps</span></span>

* <span data-ttu-id="bd4d5-170">この記事では、Facebook で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="bd4d5-171">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="bd4d5-172">Web サイトを Azure web アプリに発行したら、Facebook 開発者ポータル`AppSecret`でをリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="bd4d5-173">`Authentication:Facebook:AppId`と`Authentication:Facebook:AppSecret`を、Azure portal のアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="bd4d5-174">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="bd4d5-174">The configuration system is set up to read keys from environment variables.</span></span>
