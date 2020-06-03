---
<span data-ttu-id="9e4d8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9e4d8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9e4d8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9e4d8-102">'Blazor'</span></span>
- <span data-ttu-id="9e4d8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9e4d8-103">'Identity'</span></span>
- <span data-ttu-id="9e4d8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9e4d8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9e4d8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9e4d8-105">'Razor'</span></span>
- <span data-ttu-id="9e4d8-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9e4d8-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="9e4d8-107">Visual Studio for ASP.NET Core の開発時 IIS サポート</span><span class="sxs-lookup"><span data-stu-id="9e4d8-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="9e4d8-108">[Sourabh Shirhatti](https://twitter.com/sshirhatti) による投稿</span><span class="sxs-lookup"><span data-stu-id="9e4d8-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e4d8-109">この記事では、Windows Server 上の IIS で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://visualstudio.microsoft.com) のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9e4d8-110">このトピックでは、このシナリオを有効にしてプロジェクトを設定する手順を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e4d8-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9e4d8-111">Prerequisites</span></span>

* <span data-ttu-id="9e4d8-112">[Visual Studio (Windows 版)](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-112">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="9e4d8-113">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="9e4d8-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9e4d8-114">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="9e4d8-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9e4d8-115">X.509 セキュリティ証明書 (HTTPS のサポート用)</span><span class="sxs-lookup"><span data-stu-id="9e4d8-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9e4d8-116">IIS を有効にする</span><span class="sxs-lookup"><span data-stu-id="9e4d8-116">Enable IIS</span></span>

1. <span data-ttu-id="9e4d8-117">Windows で、 **[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9e4d8-118">**[インターネット インフォメーション サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9e4d8-119">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-119">Select **OK**.</span></span>

<span data-ttu-id="9e4d8-120">IIS のインストールには、システムの再起動が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9e4d8-121">IIS の構成</span><span class="sxs-lookup"><span data-stu-id="9e4d8-121">Configure IIS</span></span>

<span data-ttu-id="9e4d8-122">IIS には、次のように構成された Web サイトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9e4d8-123">**ホスト名**:通常、`localhost` の**ホスト名**では**既定の Web サイト**が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9e4d8-124">ただし、一意のホスト名を持つ任意の有効な IIS Web サイトが動作します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9e4d8-125">**サイト バインド**</span><span class="sxs-lookup"><span data-stu-id="9e4d8-125">**Site Binding**</span></span>
  * <span data-ttu-id="9e4d8-126">HTTPS を必要とするアプリの場合は、証明書でポート 443 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9e4d8-127">通常は、**IIS Express 開発証明書**が使用されますが、任意の有効な証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9e4d8-128">HTTP を使用するアプリの場合は、ポート 80 へのバインドが存在することを確認するか、または新しいサイト用にポート 80 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9e4d8-129">HTTP または HTTPS のいずれかに対する 1 つのバインドを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9e4d8-130">**HTTP と HTTPS の両方のポートに同時にバインドすることはサポートされていません。**</span><span class="sxs-lookup"><span data-stu-id="9e4d8-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9e4d8-131">Visual Studio の開発時 IIS サポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="9e4d8-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9e4d8-132">Visual Studio インストーラーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9e4d8-133">IIS 開発時のサポート用に使用する Visual Studio のインストールの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9e4d8-134">**ASP.NET と Web 開発**ワークロードで、**開発時 IIS サポート** コンポーネントを探してインストールします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9e4d8-135">ワークロードの右側にある **[インストールの詳細]** パネルの**開発時 IIS サポート**の下の **[省略可能]** セクションの一覧に、コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9e4d8-136">このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9e4d8-137">プロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="9e4d8-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9e4d8-138">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="9e4d8-138">HTTPS redirection</span></span>

<span data-ttu-id="9e4d8-139">HTTPS が必要な新しいプロジェクトの場合は、 **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[HTTPS 用の構成]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9e4d8-140">チェック ボックスをオンにすると、アプリの作成時に [HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)がアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9e4d8-141">HTTPS が必要な既存のプロジェクトでは、`Startup.Configure` 内で HTTPS リダイレクトと HSTS ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9e4d8-142">詳細については、「<xref:security/enforcing-ssl>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9e4d8-143">HTTP を使用するプロジェクトの場合は、[HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)はアプリに追加されません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9e4d8-144">アプリの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9e4d8-145">IIS 起動プロファイル</span><span class="sxs-lookup"><span data-stu-id="9e4d8-145">IIS launch profile</span></span>

<span data-ttu-id="9e4d8-146">新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9e4d8-147">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9e4d8-148">**[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-148">Select **Properties**.</span></span> <span data-ttu-id="9e4d8-149">**[デバッグ]** タブを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9e4d8-150">**[プロファイル]** で、 **[新規]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9e4d8-151">ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9e4d8-152">**[OK]** をクリックして、プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9e4d8-153">**[起動]** の設定で、一覧から **[IIS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9e4d8-154">**[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9e4d8-155">アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9e4d8-156">HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9e4d8-157">[前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9e4d8-158">URL の最後でアプリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9e4d8-159">たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9e4d8-160">**[環境変数]** セクションで、 **[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9e4d8-161">`ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9e4d8-162">**[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9e4d8-163">Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9e4d8-164">プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9e4d8-165">プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはインプロセスです。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="9e4d8-166">アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9e4d8-167">プロファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-167">Save the profile.</span></span>

<span data-ttu-id="9e4d8-168">Visual Studio を使用していない場合は、起動プロファイルを *Properties* フォルダーの [launchSettings.json](https://json.schemastore.org/launchsettings) ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9e4d8-169">次の例では、HTTPS プロトコルを使用するようにプロファイルを構成しています。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="9e4d8-170">`applicationUrl` エンドポイントと `launchUrl` エンドポイントが一致し、IIS バインド構成と同じプロトコル (HTTP または HTTPS) が使用されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9e4d8-171">プロジェクトを実行する</span><span class="sxs-lookup"><span data-stu-id="9e4d8-171">Run the project</span></span>

<span data-ttu-id="9e4d8-172">Visual Studio を管理者として実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9e4d8-173">ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9e4d8-174">[[デバッグの開始] ボタン](/visualstudio/debugger/debugger-feature-tour)を **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9e4d8-175">管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9e4d8-176">その場合は、Visual Studio を再起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9e4d8-177">信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4d8-178">[マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9e4d8-179">たとえば、ブレーク ポイントがヒットしません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e4d8-180">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9e4d8-180">Additional resources</span></span>

* [<span data-ttu-id="9e4d8-181">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="9e4d8-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e4d8-182">この記事では、Windows Server 上の IIS で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://visualstudio.microsoft.com) のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9e4d8-183">このトピックでは、このシナリオを有効にしてプロジェクトを設定する手順を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e4d8-184">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9e4d8-184">Prerequisites</span></span>

* <span data-ttu-id="9e4d8-185">[Visual Studio (Windows 版)](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-185">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="9e4d8-186">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="9e4d8-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9e4d8-187">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="9e4d8-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9e4d8-188">X.509 セキュリティ証明書 (HTTPS のサポート用)</span><span class="sxs-lookup"><span data-stu-id="9e4d8-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9e4d8-189">IIS を有効にする</span><span class="sxs-lookup"><span data-stu-id="9e4d8-189">Enable IIS</span></span>

1. <span data-ttu-id="9e4d8-190">Windows で、 **[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9e4d8-191">**[インターネット インフォメーション サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9e4d8-192">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-192">Select **OK**.</span></span>

<span data-ttu-id="9e4d8-193">IIS のインストールには、システムの再起動が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9e4d8-194">IIS の構成</span><span class="sxs-lookup"><span data-stu-id="9e4d8-194">Configure IIS</span></span>

<span data-ttu-id="9e4d8-195">IIS には、次のように構成された Web サイトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9e4d8-196">**ホスト名**:通常、`localhost` の**ホスト名**では**既定の Web サイト**が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9e4d8-197">ただし、一意のホスト名を持つ任意の有効な IIS Web サイトが動作します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9e4d8-198">**サイト バインド**</span><span class="sxs-lookup"><span data-stu-id="9e4d8-198">**Site Binding**</span></span>
  * <span data-ttu-id="9e4d8-199">HTTPS を必要とするアプリの場合は、証明書でポート 443 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9e4d8-200">通常は、**IIS Express 開発証明書**が使用されますが、任意の有効な証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9e4d8-201">HTTP を使用するアプリの場合は、ポート 80 へのバインドが存在することを確認するか、または新しいサイト用にポート 80 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9e4d8-202">HTTP または HTTPS のいずれかに対する 1 つのバインドを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9e4d8-203">**HTTP と HTTPS の両方のポートに同時にバインドすることはサポートされていません。**</span><span class="sxs-lookup"><span data-stu-id="9e4d8-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9e4d8-204">Visual Studio の開発時 IIS サポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="9e4d8-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9e4d8-205">Visual Studio インストーラーを起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9e4d8-206">IIS 開発時のサポート用に使用する Visual Studio のインストールの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9e4d8-207">**ASP.NET と Web 開発**ワークロードで、**開発時 IIS サポート** コンポーネントを探してインストールします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9e4d8-208">ワークロードの右側にある **[インストールの詳細]** パネルの**開発時 IIS サポート**の下の **[省略可能]** セクションの一覧に、コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9e4d8-209">このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9e4d8-210">プロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="9e4d8-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9e4d8-211">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="9e4d8-211">HTTPS redirection</span></span>

<span data-ttu-id="9e4d8-212">HTTPS が必要な新しいプロジェクトの場合は、 **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[HTTPS 用の構成]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9e4d8-213">チェック ボックスをオンにすると、アプリの作成時に [HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)がアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9e4d8-214">HTTPS が必要な既存のプロジェクトでは、`Startup.Configure` 内で HTTPS リダイレクトと HSTS ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9e4d8-215">詳細については、「<xref:security/enforcing-ssl>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9e4d8-216">HTTP を使用するプロジェクトの場合は、[HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)はアプリに追加されません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9e4d8-217">アプリの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9e4d8-218">IIS 起動プロファイル</span><span class="sxs-lookup"><span data-stu-id="9e4d8-218">IIS launch profile</span></span>

<span data-ttu-id="9e4d8-219">新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9e4d8-220">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9e4d8-221">**[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-221">Select **Properties**.</span></span> <span data-ttu-id="9e4d8-222">**[デバッグ]** タブを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9e4d8-223">**[プロファイル]** で、 **[新規]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9e4d8-224">ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9e4d8-225">**[OK]** をクリックして、プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9e4d8-226">**[起動]** の設定で、一覧から **[IIS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9e4d8-227">**[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9e4d8-228">アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9e4d8-229">HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9e4d8-230">[前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9e4d8-231">URL の最後でアプリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9e4d8-232">たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9e4d8-233">**[環境変数]** セクションで、 **[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9e4d8-234">`ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9e4d8-235">**[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9e4d8-236">Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9e4d8-237">プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9e4d8-238">プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはアウトプロセスです。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="9e4d8-239">アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9e4d8-240">プロファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-240">Save the profile.</span></span>

<span data-ttu-id="9e4d8-241">Visual Studio を使用していない場合は、起動プロファイルを *Properties* フォルダーの [launchSettings.json](https://json.schemastore.org/launchsettings) ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9e4d8-242">次の例では、HTTPS プロトコルを使用するようにプロファイルを構成しています。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="9e4d8-243">`applicationUrl` エンドポイントと `launchUrl` エンドポイントが一致し、IIS バインド構成と同じプロトコル (HTTP または HTTPS) が使用されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9e4d8-244">プロジェクトを実行する</span><span class="sxs-lookup"><span data-stu-id="9e4d8-244">Run the project</span></span>

<span data-ttu-id="9e4d8-245">Visual Studio を管理者として実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9e4d8-246">ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9e4d8-247">[[デバッグの開始] ボタン](/visualstudio/debugger/debugger-feature-tour)を **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9e4d8-248">管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9e4d8-249">その場合は、Visual Studio を再起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9e4d8-250">信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4d8-251">[マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9e4d8-252">たとえば、ブレーク ポイントがヒットしません。</span><span class="sxs-lookup"><span data-stu-id="9e4d8-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e4d8-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9e4d8-253">Additional resources</span></span>

* [<span data-ttu-id="9e4d8-254">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="9e4d8-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
