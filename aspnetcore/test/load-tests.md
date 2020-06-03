---
<span data-ttu-id="9c256-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9c256-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9c256-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9c256-102">'Blazor'</span></span>
- <span data-ttu-id="9c256-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9c256-103">'Identity'</span></span>
- <span data-ttu-id="9c256-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9c256-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9c256-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9c256-105">'Razor'</span></span>
- <span data-ttu-id="9c256-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9c256-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="9c256-107">ASP.NET Core のロード テスト/ストレス テスト</span><span class="sxs-lookup"><span data-stu-id="9c256-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="9c256-108">ロード テストとストレス テストは、Web アプリのパフォーマンスと拡張性を確保するために重要です。</span><span class="sxs-lookup"><span data-stu-id="9c256-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9c256-109">これらの目標は、よく似たテストを共有する場合でも異なります。</span><span class="sxs-lookup"><span data-stu-id="9c256-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="9c256-110">**ロード テスト**:アプリが応答の目標を満たしつつ、特定のシナリオで、指定されたユーザー負荷を処理できるかどうかをテストします。</span><span class="sxs-lookup"><span data-stu-id="9c256-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9c256-111">アプリは通常の状態で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9c256-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="9c256-112">**ストレス テスト**:厳しい条件下で、多くの場合長い時間実行したときのアプリの安定性をテストします。</span><span class="sxs-lookup"><span data-stu-id="9c256-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="9c256-113">このテストでは、急激な負荷または徐々に増加する負荷によって、アプリに高いユーザー負荷をかけます。または、アプリのコンピューティング リソースを制限します。</span><span class="sxs-lookup"><span data-stu-id="9c256-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="9c256-114">ストレス テストでは、負荷がかかっているアプリが障害から復旧し、期待される動作に適切に戻ることができるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="9c256-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="9c256-115">ストレスをかけるアプリは、通常の状況では実行されません。</span><span class="sxs-lookup"><span data-stu-id="9c256-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="9c256-116">Visual Studio 2019 は、ロード テスト機能を備えた最後のバージョンの Visual Studio です。</span><span class="sxs-lookup"><span data-stu-id="9c256-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9c256-117">今後、ロード テスト ツールを必要とするお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの代替のツールを推奨します。</span><span class="sxs-lookup"><span data-stu-id="9c256-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="9c256-118">詳細については、「[Visual Studio 2019 リリース ノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c256-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9c256-119">Visual Studio ツール</span><span class="sxs-lookup"><span data-stu-id="9c256-119">Visual Studio tools</span></span>

<span data-ttu-id="9c256-120">ユーザーは、Visual Studio を使用して Web パフォーマンス テストおよびロード テストを作成、開発、およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="9c256-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9c256-121">Web ブラウザーでアクションを記録してテストを作成するオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c256-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="9c256-122">Visual Studio 2017 を使用してロード テスト プロジェクトを作成、構成、および実行する方法については、「[クイックスタート: ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c256-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="9c256-123">ロード テストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="9c256-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9c256-124">サードパーティ製のツール</span><span class="sxs-lookup"><span data-stu-id="9c256-124">Third-party tools</span></span>

<span data-ttu-id="9c256-125">次の一覧には、さまざまな機能セットを備えたサードパーティ製の Web パフォーマンス ツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c256-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="9c256-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="9c256-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="9c256-127">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="9c256-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="9c256-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="9c256-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="9c256-129">k6</span><span class="sxs-lookup"><span data-stu-id="9c256-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="9c256-130">Locust</span><span class="sxs-lookup"><span data-stu-id="9c256-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="9c256-131">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="9c256-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="9c256-132">Netling</span><span class="sxs-lookup"><span data-stu-id="9c256-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="9c256-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="9c256-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

