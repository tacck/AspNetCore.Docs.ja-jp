---
title: ASP.NETコアでのFacebook外部ログイン設定
author: rick-anderson
description: 既存のASP.NETコアアプリへのFacebookアカウントのユーザー認証の統合を示すコード例を含むチュートリアル。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277302"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="cb4d6-103">ASP.NETコアでのFacebook外部ログイン設定</span><span class="sxs-lookup"><span data-stu-id="cb4d6-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="cb4d6-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb4d6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="cb4d6-105">このチュートリアルのコード例では、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Facebook アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="cb4d6-106">まず、[公式の手順](https://developers.facebook.com)に従ってFacebookアプリIDを作成します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="cb4d6-107">フェイスブックでアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="cb4d6-107">Create the app in Facebook</span></span>

* <span data-ttu-id="cb4d6-108">プロジェクトにパッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)追加します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="cb4d6-109">[Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="cb4d6-110">Facebookアカウントをお持ちの場合は、ログインページの**Facebookにサインアップ**リンクを使用してアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="cb4d6-111">Facebookアカウントをお持ちの場合は、指示に従ってFacebookデベロッパーとして登録します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="cb4d6-112">[**マイ アプリ]** メニューから [**アプリの作成**] を選択して新しいアプリ ID を作成します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![開発者向けフェイスブック ポータルマイクロソフト エッジで開く](index/_static/FBMyApps.png)

* <span data-ttu-id="cb4d6-114">フォームに入力し、[アプリ**ID**の作成] ボタンをタップします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![新しいアプリ ID フォームの作成](index/_static/FBNewAppId.png)

* <span data-ttu-id="cb4d6-116">新しいアプリ カードで、[**製品の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="cb4d6-117">**Facebookログイン**カードで、[セットアップ] をクリックします **。**</span><span class="sxs-lookup"><span data-stu-id="cb4d6-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![製品のセットアップ ページ](index/_static/FBProductSetup.png)

* <span data-ttu-id="cb4d6-119">**クイックスタート**ウィザードが起動し、最初のページとして**プラットフォームの選択**が行われます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="cb4d6-120">左下のメニューにある**FaceBook ログイン\*\*\*\*設定**リンクをクリックして、今のところウィザードをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![クイックスタートをスキップ](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="cb4d6-122">**[クライアント OAuth 設定]** ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="cb4d6-124">有効**な OAuth リダイレクト URI**フィールドに */signin-facebook*を追加して開発 URI`https://localhost:44320/signin-facebook`を入力します (例: )。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="cb4d6-125">このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook*ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="cb4d6-126">URI */signin-facebook*は、Facebook 認証プロバイダのデフォルトコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="cb4d6-127">既定のコールバック URI は、継承された[リモート認証オプションクラスのコールバックパス](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して[、Facebook](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)認証ミドルウェアを構成するときに変更できます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="cb4d6-128">[**Save Changes**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="cb4d6-129">左側のナビゲーションで **[基本設定** > ]**リンクを**クリックします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="cb4d6-130">このページで、あなたとあなたの`App ID``App Secret`.</span><span class="sxs-lookup"><span data-stu-id="cb4d6-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="cb4d6-131">次のセクションで、ASP.NET Core アプリケーションに両方を追加します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="cb4d6-132">サイトを展開する際には **、Facebookログイン**設定ページに再アクセスし、新しいパブリックURIを登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="cb4d6-133">FacebookアプリのIDとシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="cb4d6-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="cb4d6-134">[秘密のマネージャー](xref:security/app-secrets)で、FacebookアプリのIDや秘密の値などの機密性の高い設定を保存します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cb4d6-135">このサンプルでは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="cb4d6-136">「シークレット ストレージを有効にする」の手順に従って、[プロジェクトをシークレット ストレージ用に](xref:security/app-secrets#enable-secret-storage)初期化します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="cb4d6-137">秘密鍵とローカルシークレットストアに機密設定を保存`Authentication:Facebook:AppId`し、 `Authentication:Facebook:AppSecret`</span><span class="sxs-lookup"><span data-stu-id="cb4d6-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="cb4d6-138">フェイスブック認証の設定</span><span class="sxs-lookup"><span data-stu-id="cb4d6-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="cb4d6-139">`ConfigureServices` *Startup.cs*ファイルのメソッドにFacebookサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="cb4d6-140">フェイスブックでサインインする</span><span class="sxs-lookup"><span data-stu-id="cb4d6-140">Sign in with Facebook</span></span>

* <span data-ttu-id="cb4d6-141">アプリを実行し、[**ログイン**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="cb4d6-142">[**別のサービスを使用してログインする]** で、[Facebook] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="cb4d6-143">認証のために**Facebook**にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="cb4d6-144">Facebookの認証情報を入力します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="cb4d6-145">メールを設定できるサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="cb4d6-146">これで、Facebookの認証情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="cb4d6-147">外部サインインの承認をキャンセルする反応</span><span class="sxs-lookup"><span data-stu-id="cb4d6-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="cb4d6-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>は、ユーザーが要求された承認要求を承認しない場合に、ユーザー エージェントへのリダイレクト パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="cb4d6-149">次のコードは、 `AccessDeniedPath` `"/AccessDeniedPathInfo"`をに設定します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="cb4d6-150">このページには`AccessDeniedPath`、次の情報が含まれていることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="cb4d6-151">リモート認証が取り消されました。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="cb4d6-152">このアプリは認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-152">This app requires authentication.</span></span>
* <span data-ttu-id="cb4d6-153">サインインを再試行するには、[ログイン] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="cb4d6-154">アクセス拒否パスのテスト</span><span class="sxs-lookup"><span data-stu-id="cb4d6-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="cb4d6-155">[facebook.com](https://www.facebook.com/)に移動</span><span class="sxs-lookup"><span data-stu-id="cb4d6-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="cb4d6-156">サイン インしている場合は、サインアウトする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="cb4d6-157">アプリを実行し、Facebookサインインを選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="cb4d6-158">[**今すぐ使用しない**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-158">Select **Not now**.</span></span> <span data-ttu-id="cb4d6-159">指定した`AccessDeniedPath`ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="cb4d6-160">Facebook 認証でサポートされる設定オプションの詳細については[、FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="cb4d6-161">構成オプションは、次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="cb4d6-162">ユーザーに関するさまざまな情報を要求します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-162">Request different information about the user.</span></span>
* <span data-ttu-id="cb4d6-163">クエリ文字列引数を追加して、ログイン エクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="cb4d6-164">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="cb4d6-164">Troubleshooting</span></span>

* <span data-ttu-id="cb4d6-165">**ASP.NETコア 2.x のみ:** で IDENTITY が呼び出`services.AddIdentity``ConfigureServices`すことによって構成されていない場合、認証を試みると *、ArgumentException: 'SignInScheme' オプションを指定する必要があります*。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="cb4d6-166">このチュートリアルで使用するプロジェクト テンプレートにより、この作業が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="cb4d6-167">初期移行を適用してサイト データベースが作成されていない場合、*要求エラーの処理中にデータベース操作が失敗しました*。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="cb4d6-168">[**移行の適用]** をタップしてデータベースを作成し、更新してエラーを超えて続行します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb4d6-169">次のステップ</span><span class="sxs-lookup"><span data-stu-id="cb4d6-169">Next steps</span></span>

* <span data-ttu-id="cb4d6-170">この記事では、Facebookで認証する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="cb4d6-171">同様の方法で、[前のページ](xref:security/authentication/social/index)に記載されている他のプロバイダとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="cb4d6-172">Web サイトを Azure Web アプリに公開したら、Facebook`AppSecret`開発者ポータル で をリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="cb4d6-173">と`Authentication:Facebook:AppId`として`Authentication:Facebook:AppSecret`アプリケーションの設定を Azure ポータルで設定します。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="cb4d6-174">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="cb4d6-174">The configuration system is set up to read keys from environment variables.</span></span>
