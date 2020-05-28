## <a name="troubleshoot"></a><span data-ttu-id="3ba08-101">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3ba08-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="3ba08-102">Cookie とサイトデータ</span><span class="sxs-lookup"><span data-stu-id="3ba08-102">Cookies and site data</span></span>

<span data-ttu-id="3ba08-103">Cookie とサイトデータは、アプリの更新間で保持され、テストやトラブルシューティングに干渉する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3ba08-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="3ba08-104">アプリコードの変更、プロバイダーによるユーザーアカウントの変更、またはプロバイダーアプリの構成の変更を行うときは、次のことをオフにします。</span><span class="sxs-lookup"><span data-stu-id="3ba08-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="3ba08-105">ユーザーサインイン cookie</span><span class="sxs-lookup"><span data-stu-id="3ba08-105">User sign-in cookies</span></span>
* <span data-ttu-id="3ba08-106">アプリ cookie</span><span class="sxs-lookup"><span data-stu-id="3ba08-106">App cookies</span></span>
* <span data-ttu-id="3ba08-107">キャッシュおよび保存されたサイトデータ</span><span class="sxs-lookup"><span data-stu-id="3ba08-107">Cached and stored site data</span></span>

<span data-ttu-id="3ba08-108">残留 cookie とサイトデータがテストとトラブルシューティングに干渉しないようにする方法の1つは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3ba08-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="3ba08-109">ブラウザーを構成する</span><span class="sxs-lookup"><span data-stu-id="3ba08-109">Configure a browser</span></span>
  * <span data-ttu-id="3ba08-110">ブラウザーが閉じられるたびに cookie とサイトデータをすべて削除するように構成できるテストには、ブラウザーを使用します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-110">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="3ba08-111">ブラウザーが手動で閉じられていること、またはアプリ、テストユーザー、またはプロバイダーの構成を変更したときに IDE によって閉じられていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-111">Make sure that the browser is closed manually or by the IDE between any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="3ba08-112">カスタムコマンドを使用して、Visual Studio でブラウザーを incognito またはプライベートモードで開きます。</span><span class="sxs-lookup"><span data-stu-id="3ba08-112">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="3ba08-113">Visual Studio の [**実行**] ボタンから [**ブラウザーを使用して参照**] ダイアログボックスを開きます。</span><span class="sxs-lookup"><span data-stu-id="3ba08-113">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="3ba08-114">**[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3ba08-114">Select the **Add** button.</span></span>
  * <span data-ttu-id="3ba08-115">**Program**フィールドにブラウザーへのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-115">Provide the path to your browser in the **Program** field.</span></span>
  * <span data-ttu-id="3ba08-116">[**引数**] フィールドに、ブラウザーが incognito モードまたはプライベートモードで開くために使用するコマンドラインオプション、およびアプリの URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-116">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode and the URL of the app.</span></span> <span data-ttu-id="3ba08-117">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-117">For example:</span></span>
    * <span data-ttu-id="3ba08-118">Google Chrome:`--incognito --new-window https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3ba08-118">Google Chrome: `--incognito --new-window https://localhost:5001`</span></span>
    * <span data-ttu-id="3ba08-119">Mozilla Firefox:`-private -url https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3ba08-119">Mozilla Firefox: `-private -url https://localhost:5001`</span></span>
  * <span data-ttu-id="3ba08-120">[**フレンドリ名**] フィールドに名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-120">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="3ba08-121">たとえば、「 `Firefox Auth Testing` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-121">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="3ba08-122">**[OK]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-122">Select the **OK** button.</span></span>
  * <span data-ttu-id="3ba08-123">アプリを使用したテストの各イテレーションでブラウザープロファイルを選択する必要がないようにするには、[**既定値として設定**] ボタンを使用してプロファイルを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-123">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="3ba08-124">アプリ、テストユーザー、またはプロバイダーの構成を変更したときに、ブラウザーが IDE によって閉じられていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-124">Make sure that the browser is closed by the IDE between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="3ba08-125">サーバーアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3ba08-125">Run the Server app</span></span>

<span data-ttu-id="3ba08-126">ホステッド Blazor アプリのテストとトラブルシューティングを行うときは、**サーバー**プロジェクトからアプリを実行していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3ba08-126">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="3ba08-127">たとえば、Visual Studio で、次のいずれかの方法でアプリを起動する前に、**ソリューションエクスプローラー**でサーバープロジェクトが強調表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-127">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="3ba08-128">**[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-128">Select the **Run** button.</span></span>
* <span data-ttu-id="3ba08-129">**Debug**  >  メニューから [デバッグ] [**デバッグの開始**] を使用します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-129">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="3ba08-130"><kbd>F5</kbd>キーを押します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-130">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="3ba08-131">JSON Web トークン (JWT) の内容を検査する</span><span class="sxs-lookup"><span data-stu-id="3ba08-131">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="3ba08-132">JSON Web トークン (JWT) をデコードするには、Microsoft の[jwt.ms](https://jwt.ms/)ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="3ba08-132">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="3ba08-133">UI の値がブラウザーから離れていることはありません。</span><span class="sxs-lookup"><span data-stu-id="3ba08-133">Values in the UI never leave your browser.</span></span>
