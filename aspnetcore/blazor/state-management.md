---
<span data-ttu-id="78fc1-101">title:'ASP.NET Core Blazor 状態管理' author: description:'Blazor サーバー アプリで状態を維持する方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="78fc1-101">title: 'ASP.NET Core Blazor state management' author: description: 'Learn how to persist state in Blazor Server apps.'</span></span>
<span data-ttu-id="78fc1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="78fc1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="78fc1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="78fc1-103">'Blazor'</span></span>
- <span data-ttu-id="78fc1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="78fc1-104">'Identity'</span></span>
- <span data-ttu-id="78fc1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="78fc1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="78fc1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="78fc1-106">'Razor'</span></span>
- <span data-ttu-id="78fc1-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="78fc1-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="78fc1-108">ASP.NET Core Blazor 状態管理</span><span class="sxs-lookup"><span data-stu-id="78fc1-108">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="78fc1-109">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="78fc1-109">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="78fc1-110"> Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-110"> Server is a stateful app framework.</span></span> <span data-ttu-id="78fc1-111">ほとんどの場合、アプリではサーバーとの現在進行中の接続が維持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-111">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="78fc1-112">ユーザーの状態は、"*回線*" の中のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-112">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="78fc1-113">ユーザーの回線に保存される状態には次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-113">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="78fc1-114">レンダリングされた UI:階層になっているコンポーネント インスタンスとその最近のレンダリング出力。</span><span class="sxs-lookup"><span data-stu-id="78fc1-114">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="78fc1-115">コンポーネント インスタンスに含まれるあらゆるフィールドとプロパティの値。</span><span class="sxs-lookup"><span data-stu-id="78fc1-115">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="78fc1-116">回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータ。</span><span class="sxs-lookup"><span data-stu-id="78fc1-116">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="78fc1-117">この記事では、Blazor Server アプリの状態維持について取り扱います。</span><span class="sxs-lookup"><span data-stu-id="78fc1-117">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="78fc1-118"> WebAssembly アプリでは、[ブラウザーのクライアント側の状態維持](#client-side-in-the-browser)が利用されますが、この記事に扱う範囲を超えたカスタム ソリューションやサードパーティ製のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-118"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="78fc1-119"> 回線</span><span class="sxs-lookup"><span data-stu-id="78fc1-119"> circuits</span></span>

<span data-ttu-id="78fc1-120">ユーザーが一時的にネットワークに接続できなくなる場合、Blazor では、ユーザーがアプリを引き続き使用できるよう、そのユーザーを元の回線に再接続が試行されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-120">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="78fc1-121">ただし、サーバーのメモリにある元の回線にいつでもユーザーを再接続できるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-121">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="78fc1-122">サーバーでは、切断された回線を永久に保持することはできません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-122">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="78fc1-123">サーバーでは、タイムアウト後、あるいはサーバーがメモリ不足になったとき、切断された回線を解放しなければなりません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-123">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="78fc1-124">マルチサーバーによる負荷が分散された展開環境では、要求を処理するサーバーが突然利用不可能になることがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-124">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="78fc1-125">個々のサーバーは、それがなくても全体的な要求量を処理できるようになると、作動しなくなったり、自動的に削除されたりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-125">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="78fc1-126">ユーザーが再接続を試みたとき、元のサーバーが利用できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-126">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="78fc1-127">ユーザーはブラウザーを閉じて再び起動するか、ページを再読み込みできます。それでブラウザーのメモリに保存されている状態が削除されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-127">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="78fc1-128">たとえば、JavaScript の相互運用呼び出しによって設定された値は失われます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-128">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="78fc1-129">ユーザーが元の回線に再接続できないとき、そのユーザーには、状態が空の新しい回線が与えられます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-129">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="78fc1-130">これはデスクトップ アプリを終了してもう一度起動することと同じです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-130">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="78fc1-131">回線間で状態を維持する</span><span class="sxs-lookup"><span data-stu-id="78fc1-131">Preserve state across circuits</span></span>

<span data-ttu-id="78fc1-132">シナリオによっては、回線間で状態を維持することが望ましい場合もあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-132">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="78fc1-133">次の場合、アプリでは、ユーザーの重要なデータを保持できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-133">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="78fc1-134">Web サーバーが利用できなくなる。</span><span class="sxs-lookup"><span data-stu-id="78fc1-134">The web server becomes unavailable.</span></span>
* <span data-ttu-id="78fc1-135">ユーザーのブラウザーが、新しい Web サーバーで新しい回線を始めるように強制される。</span><span class="sxs-lookup"><span data-stu-id="78fc1-135">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="78fc1-136">一般的に、ユーザーが既存のデータを読み取るだけでなく、積極的にデータを作成するとき、回線間の状態の維持が適用されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-136">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="78fc1-137">1 つの回線を超えて状態を維持するには、"*データをサーバーのメモリに保存するだけでは不十分です*"。</span><span class="sxs-lookup"><span data-stu-id="78fc1-137">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="78fc1-138">アプリでは、データを他の保管場所にも保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-138">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="78fc1-139">状態は自動的に保存されません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-139">State persistence isn't automatic.</span></span> <span data-ttu-id="78fc1-140">ステートフルなデータ保存を実装するアプリを開発するとき、措置を講じる必要があります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-140">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="78fc1-141">データの永続性は通常、その状態を作り出すためにユーザーが労力を費やす、高価値の状態にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-141">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="78fc1-142">次の例では、状態を維持することで、時間が節約され、商業活動の支援になります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-142">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="78fc1-143">マルチステップ Web フォーム:マルチステップ プロセスのいくつかの手順を完了したのに、状態が失われたためにデータを再入力しなければならないのは、ユーザーにとって時間の浪費になります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-143">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="78fc1-144">このシナリオでは、マルチステップ フォームからユーザーが離れ、後で戻ってきた場合、状態が失われます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-144">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="78fc1-145">ショッピング カート:収益につながる可能性がある、アプリの中のビジネス上重要なコンポーネントを維持できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-145">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="78fc1-146">ユーザーが自分の状態を失い、そのため、自分のショッピング カートが消えると、後でサイトに戻ってきたとき、製品やサービスの購入数が減ることがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-146">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="78fc1-147">簡単に再現される状態は通常、保存する必要がありません。サインイン ダイアログに入力されたユーザー名が送信されていない場合などです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-147">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78fc1-148">アプリでは、"*アプリの状態*" のみが維持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-148">An app can only persist *app state*.</span></span> <span data-ttu-id="78fc1-149">コンポーネント インスタンスやそのレンダー ツリーなど、UI は維持されません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-149">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="78fc1-150">コンポーネントとレンダー ツリーは一般的にシリアル化されません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-150">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="78fc1-151">展開された TreeView ノードなど、UI の状態に似た何かを維持するには、シリアル化できるアプリ状態として動作をモデル化するためのカスタム コードをアプリに与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-151">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="78fc1-152">状態を維持する場所</span><span class="sxs-lookup"><span data-stu-id="78fc1-152">Where to persist state</span></span>

<span data-ttu-id="78fc1-153">Blazor Server アプリには、一般に、状態を保存する場所が 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-153">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="78fc1-154">どの手法にもそれに最も適したケースがあり、注意すべきことも異なります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-154">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="78fc1-155">データベースのサーバー側</span><span class="sxs-lookup"><span data-stu-id="78fc1-155">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="78fc1-156">URL</span><span class="sxs-lookup"><span data-stu-id="78fc1-156">URL</span></span>](#url)
* [<span data-ttu-id="78fc1-157">ブラウザーのクライアント側</span><span class="sxs-lookup"><span data-stu-id="78fc1-157">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="78fc1-158">データベースのサーバー側</span><span class="sxs-lookup"><span data-stu-id="78fc1-158">Server-side in a database</span></span>

<span data-ttu-id="78fc1-159">データを永続的に維持したり、あるデータを複数のユーザーやデバイスで使用したりするときは、独立したサーバー側データセットがおそらく最善の選択肢です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-159">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="78fc1-160">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-160">Options include:</span></span>

* <span data-ttu-id="78fc1-161">リレーショナル SQL データベース</span><span class="sxs-lookup"><span data-stu-id="78fc1-161">Relational SQL database</span></span>
* <span data-ttu-id="78fc1-162">キー値ストア</span><span class="sxs-lookup"><span data-stu-id="78fc1-162">Key-value store</span></span>
* <span data-ttu-id="78fc1-163">BLOB ストア</span><span class="sxs-lookup"><span data-stu-id="78fc1-163">Blob store</span></span>
* <span data-ttu-id="78fc1-164">テーブル ストア</span><span class="sxs-lookup"><span data-stu-id="78fc1-164">Table store</span></span>

<span data-ttu-id="78fc1-165">データがデータベースに保存されたら、ユーザーはいつでも新しい回線を開始できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-165">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="78fc1-166">ユーザーのデータは保存され、あらゆる新しい回線で利用が可能です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-166">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="78fc1-167">Azure データ ストレージ オプションに関する詳細については、「[Azure Storage のドキュメント](/azure/storage/)」と[Azure のデータベース](https://azure.microsoft.com/product-categories/databases/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="78fc1-167">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="78fc1-168">URL</span><span class="sxs-lookup"><span data-stu-id="78fc1-168">URL</span></span>

<span data-ttu-id="78fc1-169">ナビゲーションの状態を表わす一時的なデータについては、URL の一部としてデータをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-169">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="78fc1-170">URL でモデル化された状態の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-170">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="78fc1-171">表示されるエンティティの ID。</span><span class="sxs-lookup"><span data-stu-id="78fc1-171">The ID of a viewed entity.</span></span>
* <span data-ttu-id="78fc1-172">ページ付きグリッドでの現在のページ番号。</span><span class="sxs-lookup"><span data-stu-id="78fc1-172">The current page number in a paged grid.</span></span>

<span data-ttu-id="78fc1-173">次の場合、ブラウザーのアドレス バーのコンテンツが保持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-173">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="78fc1-174">ユーザーがページを手動で再読み込みした。</span><span class="sxs-lookup"><span data-stu-id="78fc1-174">If the user manually reloads the page.</span></span>
* <span data-ttu-id="78fc1-175">Web サーバーが利用できなくなり、別のサーバーに接続する目的で、ページの再読み込みがユーザーに強制される。</span><span class="sxs-lookup"><span data-stu-id="78fc1-175">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="78fc1-176">`@page` ディレクティブで URL パターンを定義する方法については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78fc1-176">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="78fc1-177">ブラウザーのクライアント側</span><span class="sxs-lookup"><span data-stu-id="78fc1-177">Client-side in the browser</span></span>

<span data-ttu-id="78fc1-178">ユーザーが一時的なデータを頻繁に作成する場合、一般的なバッキング ストアはブラウザーの `localStorage` コレクションと `sessionStorage` コレクションになります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-178">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="78fc1-179">回線が放棄された場合、保存されている状態を管理したり、消去したりすることはアプリに求められません。これはサーバー側ストレージより優れている点です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-179">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="78fc1-180">このセクションの "クライアント側" は、[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)ではなく、ブラウザーのクライアント側シナリオを指します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-180">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="78fc1-181">`localStorage` と `sessionStorage` は Blazor WebAssembly アプリで使用できますが、カスタム コードを記述するか、サードパーティ製のパッケージを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-181">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="78fc1-182">`localStorage` と `sessionStorage` は次の点で異なります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-182">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="78fc1-183">`localStorage` は範囲がユーザーのブラウザーに設定されています。</span><span class="sxs-lookup"><span data-stu-id="78fc1-183">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="78fc1-184">ユーザーがページを再読み込みするか、ブラウザーを閉じて再び開くと、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-184">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="78fc1-185">ユーザーが複数のブラウザー タブを開くと、状態はすべてのタブで同じになります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-185">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="78fc1-186">データは直接消去されるまで `localStorage` に残ります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-186">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="78fc1-187">`sessionStorage` は範囲がユーザーのブラウザー タブに設定されています。ユーザーがタブを再読み込みすると、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-187">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="78fc1-188">ユーザーがタブかブラウザーを閉じると、状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-188">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="78fc1-189">ユーザーが複数のブラウザー タブを開くと、それぞれのタブには、他に依存しないそのタブだけのバージョンのデータが保持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-189">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="78fc1-190">一般に、`sessionStorage` を使用しておけば安全です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-190">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="78fc1-191">`sessionStorage` の場合、ユーザーが複数のタブを開き、以下に遭遇するリスクが回避されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-191">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="78fc1-192">タブ間の状態保存に含まれるバグ。</span><span class="sxs-lookup"><span data-stu-id="78fc1-192">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="78fc1-193">タブで他のタブの状態が上書きされるときの紛らわしい動作。</span><span class="sxs-lookup"><span data-stu-id="78fc1-193">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="78fc1-194">ブラウザーを閉じて再び開いても状態を維持することがアプリに求められる場合、`localStorage` が最善の選択肢です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-194">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="78fc1-195">ブラウザー ストレージ使用時の注意事項:</span><span class="sxs-lookup"><span data-stu-id="78fc1-195">Caveats for using browser storage:</span></span>

* <span data-ttu-id="78fc1-196">サーバー側データベースの使用に似ていますが、データの読み込みと保存は非同期です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-196">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="78fc1-197">サーバー側データベースとは異なり、プリレンダリング中はストレージを利用できません。プリレンダリング中は、要求されたページがブラウザーに存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-197">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="78fc1-198">Blazor Server アプリの場合、数キロバイトのデータをストレージに保持するのが妥当です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-198">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="78fc1-199">数キロバイトを超えると、パフォーマンスに影響が出ることを考慮する必要があります。ネットワーク中でデータが読み込まれ、保存されるためです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-199">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="78fc1-200">ユーザーはデータを見たり、改ざんしたりするかもしれません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-200">Users may view or tamper with the data.</span></span> <span data-ttu-id="78fc1-201">ASP.NET Core [データ保護](xref:security/data-protection/introduction)でこのリスクを軽減できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-201">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="78fc1-202">サードパーティ製ブラウザーのストレージ ソリューション</span><span class="sxs-lookup"><span data-stu-id="78fc1-202">Third-party browser storage solutions</span></span>

<span data-ttu-id="78fc1-203">サードパーティ製 NuGet パッケージからは、`localStorage` と `sessionStorage` を使用するための API が与えられます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-203">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="78fc1-204">ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択してみることもお勧めします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-204">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="78fc1-205">ASP.NET Core データ保護では、保存中のデータが暗号化され、改ざんされるという潜在的リスクが減ります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-205">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="78fc1-206">JSON でシリアル化されたデータがプレーンテキストで保存されている場合、ユーザーはブラウザー開発者ツールでデータを表示できます。また、保存中のデータを変更できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-206">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="78fc1-207">データが性質上、取るに足りないものであれば、データ セキュリティを問題にする必要はないかもしれません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-207">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="78fc1-208">たとえば、UI 要素に保存されている色を読み取られたり、変更されたりしたところで、それはユーザーや組織にとって大きなセキュリティ リスクではありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-208">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="78fc1-209">"*取り扱いに慎重を要するデータ*" を見たり、改ざんしたりすることをユーザーに禁止します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-209">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="78fc1-210">Protected Browser Storage 試験用パッケージ</span><span class="sxs-lookup"><span data-stu-id="78fc1-210">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="78fc1-211">NuGet パッケージには `localStorage` や `sessionStorage` の[データを保護する](xref:security/data-protection/introduction)ものがありますが、その一例が [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-211">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="78fc1-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` はサポートのない試験用パッケージであり、現時点では運用環境での使用に適していません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="78fc1-213">インストール</span><span class="sxs-lookup"><span data-stu-id="78fc1-213">Installation</span></span>

<span data-ttu-id="78fc1-214">`Microsoft.AspNetCore.ProtectedBrowserStorage` パッケージをインストールするには:</span><span class="sxs-lookup"><span data-stu-id="78fc1-214">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="78fc1-215">Blazor Server アプリ プロジェクトで、[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-215">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="78fc1-216">最上位 HTML (たとえば、デフォルト プロジェクト テンプレートの *Pages/_Host.cshtml* ファイルで) で、次の `<script>` タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-216">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="78fc1-217">`Startup.ConfigureServices` メソッドで、`AddProtectedBrowserStorage` を呼び出し、`localStorage` サービスと `sessionStorage` サービスをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-217">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="78fc1-218">コンポーネント内でデータを保存し、読み込む</span><span class="sxs-lookup"><span data-stu-id="78fc1-218">Save and load data within a component</span></span>

<span data-ttu-id="78fc1-219">データを読み込むか、ブラウザー ストレージに保存する必要があるあらゆるコンポーネントで、[`@inject`](xref:mvc/views/razor#inject) を使用し、次のいずれかのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-219">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="78fc1-220">選択肢は、使用するバッキング ストアによって決まります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-220">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="78fc1-221">次の例では、`sessionStorage` が使用されています。</span><span class="sxs-lookup"><span data-stu-id="78fc1-221">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="78fc1-222">`@using` ステートメントは、コンポーネントの代わりに、 *_Imports.razor* ファイルに入れることができます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-222">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="78fc1-223">*_Imports.razor* ファイルを使用すると、アプリの中の大きなセグメントで、あるいはアプリ全体で名前空間を利用できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-223">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="78fc1-224">プロジェクト テンプレートの `Counter` コンポーネントに `currentCount` 値を保持するには、`ProtectedSessionStore.SetAsync` を使用するように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-224">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="78fc1-225">もっと大規模で現実に即したアプリの場合、個々のフィールドを保管するというのはありそうにないシナリオです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-225">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="78fc1-226">アプリでは多くの場合、状態が複雑なモデル オブジェクト全体を保存します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-226">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="78fc1-227">`ProtectedSessionStore` では、JSON データが自動的にシリアル化され、逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-227">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="78fc1-228">前のコード例では、`currentCount` データは、ユーザーのブラウザーに `sessionStorage['count']` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-228">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="78fc1-229">データはプレーンテキストに保存されず、ASP.NET Core の[データ保護](xref:security/data-protection/introduction)で保護されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-229">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="78fc1-230">ブラウザーの開発者コンソールで `sessionStorage['count']` が評価されるとき、暗号化されたデータを確認できることがあります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-230">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="78fc1-231">ユーザーが後で `Counter` コンポーネントに戻ったときに `currentCount` データを回復するには (まったく新しい回線にいる場合も含め)、`ProtectedSessionStore.GetAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-231">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="78fc1-232">コンポーネントのパラメーターにナビゲーションの状態が含まれている場合、`ProtectedSessionStore.GetAsync` を呼び出し、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ではなく、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> で結果を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-232">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="78fc1-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> は、コンポーネントが最初にインスタンス化されたときに 1 回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="78fc1-234">後で、その同じページにいるとき、ユーザーが別の URL に移動しても <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> が再び呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-234"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="78fc1-235">詳細については、「<xref:blazor/lifecycle>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78fc1-235">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="78fc1-236">このセクションの例は、サーバーでプリレンダリングが有効になっていない場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-236">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="78fc1-237">プリレンダリングが有効になっている場合、次のようなエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-237">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="78fc1-238">JavaScript 相互運用呼び出しは、この時点では発行できません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-238">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="78fc1-239">コンポーネントが事前レンダリングされているからです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-239">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="78fc1-240">プリレンダリングを無効にするか、プリレンダリングで使用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-240">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="78fc1-241">プリレンダリングと連動するコードを記述する方法の詳細については、「[プリレンダリングを処理する](#handle-prerendering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78fc1-241">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="78fc1-242">読み込み状態を処理する</span><span class="sxs-lookup"><span data-stu-id="78fc1-242">Handle the loading state</span></span>

<span data-ttu-id="78fc1-243">ブラウザー ストレージは非同期であるため (ネットワーク接続でアクセスされるため)、データが読み込まれ、コンポーネントで利用できるようになるまで常に一定の時間があります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-243">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="78fc1-244">最良の結果を得るには、空のデータや既定のデータを表示するのではなく、読み込みが進行中のとき、読み込み状態メッセージをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-244">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="78fc1-245">1 つの手法は、データが `null` (読み込み中) かどうかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-245">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="78fc1-246">既定の `Counter` コンポーネントでは、カウントは `int` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-246">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="78fc1-247">型 (`int`) に疑問符 (`?`) を追加し、`currentCount` を null 許容にします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-247">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="78fc1-248">カウントや **[インクリメント]** ボタンを無条件で表示するのではなく、データが読み込まれる場合にのみこれらの要素を表示するように選択します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-248">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="78fc1-249">プリレンダリングを処理する</span><span class="sxs-lookup"><span data-stu-id="78fc1-249">Handle prerendering</span></span>

<span data-ttu-id="78fc1-250">プリレンダリング中:</span><span class="sxs-lookup"><span data-stu-id="78fc1-250">During prerendering:</span></span>

* <span data-ttu-id="78fc1-251">ユーザーのブラウザーに対話式で接続することはありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-251">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="78fc1-252">ブラウザーには、JavaScript コードを実行できるページがまだありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-252">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="78fc1-253">`localStorage` または `sessionStorage` は、プリレンダリング中に使用できません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-253">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="78fc1-254">コンポーネントでストレージとのやりとりが試みられると、次のようなエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-254">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="78fc1-255">JavaScript 相互運用呼び出しは、この時点では発行できません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-255">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="78fc1-256">コンポーネントが事前レンダリングされているからです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-256">This is because the component is being prerendered.</span></span>

<span data-ttu-id="78fc1-257">このエラーを解決する方法の 1 つは、プリレンダリングを無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-257">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="78fc1-258">これは通常、ブラウザーベースのストレージがアプリで頻繁に使用される場合、最良の選択肢となります。</span><span class="sxs-lookup"><span data-stu-id="78fc1-258">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="78fc1-259">プリレンダリングによってさらに複雑になり、アプリにとっては良いことがありません。アプリでは `localStorage` または `sessionStorage` が利用できなければ、役に立つコンテンツをプリレンダリングできないからです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-259">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="78fc1-260">プリレンダリングを無効にするには、*Pages/_Host cshtml* ファイルを開き、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)の `render-mode` を <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> に変更します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-260">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="78fc1-261">プリレンダリングは、`localStorage` や `sessionStorage` を使用しない他のページでは役に立つかもしれません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-261">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="78fc1-262">プリレンダリングを有効にしておくには、ブラウザーが回線に接続されるまで読み込み操作を延期します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-262">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="78fc1-263">次はカウンター値を格納する例です。</span><span class="sxs-lookup"><span data-stu-id="78fc1-263">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="78fc1-264">状態保存を取り出し、共通の場所に入れる</span><span class="sxs-lookup"><span data-stu-id="78fc1-264">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="78fc1-265">さまざまなコンポーネントがブラウザーベースのストレージに依存しているとき、状態プロバイダー コードを何回も再実装すると、コードが重複します。</span><span class="sxs-lookup"><span data-stu-id="78fc1-265">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="78fc1-266">コードの重複を回避する選択肢の 1 つは、状態プロバイダー ロジックをカプセル化する "*状態プロバイダーの親コンポーネント*" を作成することです。</span><span class="sxs-lookup"><span data-stu-id="78fc1-266">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="78fc1-267">状態保存メカニズムに関係なく、子コンポーネントは永続保存データとやりとりできます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-267">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="78fc1-268">`CounterStateProvider` コンポーネントの次の例では、カウンター データが永続保存されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-268">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="78fc1-269">`CounterStateProvider` コンポーネントによって読み込み段階が処理されます。読み込みが完了するまで、その子コンテンツがレンダリングされることはありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-269">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="78fc1-270">`CounterStateProvider` コンポーネントを使用するには、カウンター状態にアクセスする必要がある他のコンポーネントをコンポーネントのインスタンスでラップします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-270">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="78fc1-271">アプリに含まれるすべてのコンポーネントが状態にアクセスできるようにするには、`App` コンポーネント (*App.razor*) で <xref:Microsoft.AspNetCore.Components.Routing.Router> を `CounterStateProvider` コンポーネントでラップします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-271">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="78fc1-272">ラップされたコンポーネントの元に永続化されたカウンター状態が届くので、それを変更できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-272">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="78fc1-273">次の `Counter` コンポーネントはパターンが実装されています。</span><span class="sxs-lookup"><span data-stu-id="78fc1-273">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="78fc1-274">このコンポーネントは `ProtectedBrowserStorage` とやりとりするために必要ありませんし、"読み込み" 段階にも関係ありません。</span><span class="sxs-lookup"><span data-stu-id="78fc1-274">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="78fc1-275">前に説明したようにプリレンダリングを扱うには、カウンター データを利用するすべてのコンポーネントが自動的にプリレンダリングを使用するよう、`CounterStateProvider` を変更できます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-275">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="78fc1-276">詳細については、「[プリレンダリングを処理する](#handle-prerendering)」セクションを参照してください</span><span class="sxs-lookup"><span data-stu-id="78fc1-276">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="78fc1-277">一般的に、次の場合、"*状態プロバイダーの親コンポーネント*" パターンが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="78fc1-277">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="78fc1-278">他にもさまざまなコンポーネントで状態を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="78fc1-278">To consume state in many other components.</span></span>
* <span data-ttu-id="78fc1-279">最上位の状態オブジェクトを 1 つだけ保持する場合。</span><span class="sxs-lookup"><span data-stu-id="78fc1-279">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="78fc1-280">さまざまな状態オブジェクトを保持し、さまざまな場所でさまざまなオブジェクト サブセットを使用するには、グローバルに状態を読み込み、保存することを回避することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="78fc1-280">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
