---
title: ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3430d842b6a4f7da30370977f72e6f132e28bb7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634255"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="2282c-103">ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ</span><span class="sxs-lookup"><span data-stu-id="2282c-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="2282c-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2282c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2282c-105">このサンプルでは、 [前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが職場、学校、または personal Microsoft アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2282c-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="2282c-106">Microsoft 開発者ポータルでアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2282c-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="2282c-107">プロジェクトに、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2282c-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="2282c-108">[Azure portal アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。</span><span class="sxs-lookup"><span data-stu-id="2282c-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="2282c-109">Microsoft アカウントがない場合は、[ **作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2282c-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="2282c-110">サインインすると、[ **アプリの登録** ] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2282c-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="2282c-111">**新しい登録**の選択</span><span class="sxs-lookup"><span data-stu-id="2282c-111">Select **New registration**</span></span>
* <span data-ttu-id="2282c-112">**[名前]** を入力します。</span><span class="sxs-lookup"><span data-stu-id="2282c-112">Enter a **Name**.</span></span>
* <span data-ttu-id="2282c-113">**サポートされているアカウントの種類**のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="2282c-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="2282c-114">[ **リダイレクト URI**] に、追加した開発 URL を入力し `/signin-microsoft` ます。</span><span class="sxs-lookup"><span data-stu-id="2282c-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="2282c-115">たとえば、「 `https://localhost:5001/signin-microsoft` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2282c-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="2282c-116">このサンプルの後半で構成されている Microsoft 認証スキームは、OAuth フローを実装するために、ルートで要求を自動的に処理し `/signin-microsoft` ます。</span><span class="sxs-lookup"><span data-stu-id="2282c-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="2282c-117">**[登録]** を選択します</span><span class="sxs-lookup"><span data-stu-id="2282c-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="2282c-118">クライアント シークレットを作成する</span><span class="sxs-lookup"><span data-stu-id="2282c-118">Create client secret</span></span>

* <span data-ttu-id="2282c-119">左側のウィンドウで、 **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2282c-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="2282c-120">[**クライアントシークレット**] で、[**新しいクライアントシークレット**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2282c-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="2282c-121">クライアントシークレットの説明を追加します。</span><span class="sxs-lookup"><span data-stu-id="2282c-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="2282c-122">**[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2282c-122">Select the **Add** button.</span></span>

* <span data-ttu-id="2282c-123">[ **クライアントシークレット**] で、クライアントシークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2282c-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="2282c-124">URI セグメント `/signin-microsoft` は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="2282c-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="2282c-125">[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Microsoft 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="2282c-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="2282c-126">Microsoft クライアント ID とシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="2282c-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="2282c-127">Microsoft のクライアント ID やシークレットの値など、機微な設定を [シークレットマネージャー](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="2282c-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2282c-128">このサンプルでは、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="2282c-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="2282c-129">「 [シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="2282c-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="2282c-130">秘密キーとシークレットキーを使用して、機密設定をローカルシークレットストアに保存 `Authentication:Microsoft:ClientId` し `Authentication:Microsoft:ClientSecret` ます。</span><span class="sxs-lookup"><span data-stu-id="2282c-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="2282c-131">Microsoft アカウント認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2282c-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="2282c-132">Microsoft アカウントサービスをに追加し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2282c-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="2282c-133">Microsoft アカウント認証でサポートされる構成オプションの詳細については、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2282c-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="2282c-134">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="2282c-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="2282c-135">Microsoft アカウントでサインインアカウント</span><span class="sxs-lookup"><span data-stu-id="2282c-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="2282c-136">アプリを実行し、[ **ログイン**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2282c-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="2282c-137">Microsoft でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2282c-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="2282c-138">[Microsoft] をクリックすると、認証のために Microsoft にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2282c-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="2282c-139">Microsoft アカウントでサインインすると、アプリが情報にアクセスできるようにするように求めるメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2282c-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="2282c-140">**[はい]** をタップすると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2282c-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="2282c-141">これで、Microsoft 資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="2282c-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="2282c-142">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2282c-142">Troubleshooting</span></span>

* <span data-ttu-id="2282c-143">Microsoft アカウントプロバイダーによってサインインエラーページが表示された場合は、 `#` Uri の (ハッシュタグ) のすぐ後にあるエラータイトルと説明のクエリ文字列パラメーターを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2282c-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="2282c-144">エラーメッセージは Microsoft 認証に問題があることを示していますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定されている**リダイレクト uri**と一致していないことです。</span><span class="sxs-lookup"><span data-stu-id="2282c-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="2282c-145">Identityでを呼び出すことによって構成されていない場合 `services.AddIdentity` `ConfigureServices` 、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="2282c-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="2282c-146">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="2282c-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="2282c-147">初期移行を適用してサイトデータベースが作成されていない場合は、 *要求エラーの処理中にデータベース操作が失敗* します。</span><span class="sxs-lookup"><span data-stu-id="2282c-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="2282c-148">[ **移行の適用** ] をタップしてデータベースを作成し、更新してエラーを続行します。</span><span class="sxs-lookup"><span data-stu-id="2282c-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2282c-149">次のステップ</span><span class="sxs-lookup"><span data-stu-id="2282c-149">Next steps</span></span>

* <span data-ttu-id="2282c-150">この記事では、Microsoft で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="2282c-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="2282c-151">同様のアプローチに従って、 [前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2282c-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="2282c-152">Web サイトを Azure web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアントシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2282c-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="2282c-153">とを `Authentication:Microsoft:ClientId` 、 `Authentication:Microsoft:ClientSecret` Azure portal のアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="2282c-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="2282c-154">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="2282c-154">The configuration system is set up to read keys from environment variables.</span></span>
