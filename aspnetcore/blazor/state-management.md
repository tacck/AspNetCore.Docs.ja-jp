---
title: ASP.NET Core Blazor 状態管理
author: guardrex
description: Blazor Server アプリで状態を維持する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: a74f056447839c4cf057948f26a9ece9b5799656
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606703"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="dd9d8-103">ASP.NET Core Blazor 状態管理</span><span class="sxs-lookup"><span data-stu-id="dd9d8-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="dd9d8-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd9d8-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="dd9d8-105">Blazor WebAssembly アプリで作成されたユーザー状態は、ブラウザーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="dd9d8-106">ブラウザーのメモリに保持されるユーザー状態の例としては、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="dd9d8-107">レンダリングされた UI での、コンポーネント インスタンスの階層と最新のレンダリング出力。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="dd9d8-108">コンポーネント インスタンスに含まれるフィールドとプロパティの値。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="dd9d8-109">[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保持されているデータ。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="dd9d8-110">[JavaScript 相互運用](xref:blazor/call-javascript-from-dotnet)の呼び出しによって設定された値。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="dd9d8-111">ユーザーがブラウザーを閉じて開き直したり、ページを再度読み込んだりすると、ブラウザーのメモリに保持されているユーザー状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="dd9d8-112">ブラウザー セッション間で状態を保持する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-112">Persist state across browser sessions</span></span>

<span data-ttu-id="dd9d8-113">一般に、ユーザーが既存データを単に読み取るのではなく、活発にデータを作成している場合は、ブラウザー セッション間で状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="dd9d8-114">ブラウザー セッション間で状態を維持するには、アプリで、ブラウザーのメモリとは異なる他の保存場所に、データを保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="dd9d8-115">状態は自動的に保存されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-115">State persistence isn't automatic.</span></span> <span data-ttu-id="dd9d8-116">ステートフルなデータ保存を実装するアプリを開発するとき、措置を講じる必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="dd9d8-117">データの保持は、通常、作成するためにユーザーが大きな労力を費やした、価値の高い状態の場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="dd9d8-118">次の例では、状態を維持することで、時間が節約され、商業活動の支援になります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="dd9d8-119">複数ステップの Web フォーム: 複数ステップの Web フォームのいくつかのステップを完了した後で、状態が失われた場合、ユーザーがデータを再入力するのでは時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="dd9d8-120">このシナリオでは、フォームからユーザーが離れて後で戻ってきた場合、状態が失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="dd9d8-121">ショッピング カート: 収益につながる可能性がある、アプリの中のビジネス上重要なコンポーネントを維持できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="dd9d8-122">ユーザーが自分の状態を失い、そのため、自分のショッピング カートが消えると、後でサイトに戻ってきたとき、製品やサービスの購入数が減ることがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="dd9d8-123">アプリでは、"*アプリの状態*" のみが維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-123">An app can only persist *app state*.</span></span> <span data-ttu-id="dd9d8-124">コンポーネント インスタンスやそのレンダー ツリーなど、UI は維持されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="dd9d8-125">コンポーネントとレンダー ツリーは一般的にシリアル化されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="dd9d8-126">アプリで、ツリー ビュー コントロールの展開されたノードなど、UI の状態を維持するには、カスタム コードを使用して、UI 状態の動作をシリアル化できるアプリ状態としてモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="dd9d8-127">状態を維持する場所</span><span class="sxs-lookup"><span data-stu-id="dd9d8-127">Where to persist state</span></span>

<span data-ttu-id="dd9d8-128">一般に、状態を維持するために、次のような場所があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-128">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="dd9d8-129">サーバー側ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="dd9d8-130">URL</span><span class="sxs-lookup"><span data-stu-id="dd9d8-130">URL</span></span>](#url)
* [<span data-ttu-id="dd9d8-131">ブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-131">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="dd9d8-132">メモリ内状態コンテナー サービス</span><span class="sxs-lookup"><span data-stu-id="dd9d8-132">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="dd9d8-133">サーバー側ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-133">Server-side storage</span></span>

<span data-ttu-id="dd9d8-134">複数のユーザーとデバイスにまたがって永続的にデータを保持する場合は、Web API を使用してアクセスされる独立したサーバー側ストレージをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-134">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="dd9d8-135">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-135">Options include:</span></span>

* <span data-ttu-id="dd9d8-136">BLOB ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-136">Blob storage</span></span>
* <span data-ttu-id="dd9d8-137">キーと値のストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-137">Key-value storage</span></span>
* <span data-ttu-id="dd9d8-138">リレーショナル データベース</span><span class="sxs-lookup"><span data-stu-id="dd9d8-138">Relational database</span></span>
* <span data-ttu-id="dd9d8-139">テーブル ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-139">Table storage</span></span>

<span data-ttu-id="dd9d8-140">データが保存された後は、ユーザーの状態は保持されていて、新しいブラウザー セッションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-140">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="dd9d8-141">Blazor WebAssembly アプリはユーザーのブラウザー内で完全に実行されるため、ストレージ サービスやデータベースなど、セキュリティで保護された外部システムにアクセスするには、追加の手段が必要です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-141">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="dd9d8-142">Blazor WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-142">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="dd9d8-143">通常、アプリでは、[OAuth](https://oauth.net) や [OpenID Connect (OIDC)](https://openid.net/connect/) を使用してユーザーの認証を行った後、サーバー側アプリへの Web API 呼び出しを使用して、ストレージ サービスやデータベースとやり取りします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-143">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="dd9d8-144">Blazor WebAssembly アプリとストレージ サービスまたはデータベースの間のデータ転送は、サーバー側アプリによって仲介されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-144">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="dd9d8-145">Blazor WebAssembly アプリではサーバー側アプリへの一時的な接続を維持し、サーバー側アプリではストレージへの永続的な接続が保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-145">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="dd9d8-146">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-146">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="dd9d8-147">Blazor の "*セキュリティと Identity* " に関する記事</span><span class="sxs-lookup"><span data-stu-id="dd9d8-147">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="dd9d8-148">Azure のデータ ストレージ オプションの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-148">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="dd9d8-149">Azure のデータベース</span><span class="sxs-lookup"><span data-stu-id="dd9d8-149">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="dd9d8-150">Azure Storage のドキュメント</span><span class="sxs-lookup"><span data-stu-id="dd9d8-150">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="dd9d8-151">URL</span><span class="sxs-lookup"><span data-stu-id="dd9d8-151">URL</span></span>

<span data-ttu-id="dd9d8-152">ナビゲーションの状態を表わす一時的なデータについては、URL の一部としてデータをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-152">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="dd9d8-153">たとえば、次のようなユーザー状態が URL でモデル化されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-153">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="dd9d8-154">表示されるエンティティの ID。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-154">The ID of a viewed entity.</span></span>
* <span data-ttu-id="dd9d8-155">ページ付きグリッドでの現在のページ番号。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-155">The current page number in a paged grid.</span></span>

<span data-ttu-id="dd9d8-156">ユーザーが手動でページを再度読み込むと、ブラウザーのアドレス バーの内容が保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-156">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="dd9d8-157">[`@page`](xref:mvc/views/razor#page) ディレクティブで URL パターンを定義する方法については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-157">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="dd9d8-158">ブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-158">Browser storage</span></span>

<span data-ttu-id="dd9d8-159">ユーザーが頻繁に作成する一時的なデータの場合、一般的に使用されるストレージの場所は、ブラウザーの [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) コレクションと [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) コレクションです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-159">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="dd9d8-160">`localStorage` の対象範囲はブラウザーのウィンドウです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-160">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="dd9d8-161">ユーザーがページを再読み込みするか、ブラウザーを閉じて再び開くと、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-161">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="dd9d8-162">ユーザーが複数のブラウザー タブを開くと、状態はすべてのタブで同じになります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-162">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="dd9d8-163">データは直接消去されるまで `localStorage` に残ります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-163">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="dd9d8-164">`sessionStorage` の対象範囲はブラウザーのタブです。ユーザーがタブを再読み込みすると、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-164">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="dd9d8-165">ユーザーがタブかブラウザーを閉じると、状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-165">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="dd9d8-166">ユーザーが複数のブラウザー タブを開くと、それぞれのタブには、他に依存しないそのタブだけのバージョンのデータが保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-166">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="dd9d8-167">`localStorage` と `sessionStorage` は Blazor WebAssembly アプリで使用できますが、カスタム コードを記述するか、サードパーティのパッケージを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-167">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="dd9d8-168">一般に、`sessionStorage` を使用しておけば安全です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-168">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="dd9d8-169">`sessionStorage` の場合、ユーザーが複数のタブを開き、以下に遭遇するリスクが回避されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-169">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="dd9d8-170">タブ間の状態保存に含まれるバグ。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-170">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="dd9d8-171">タブで他のタブの状態が上書きされるときの紛らわしい動作。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-171">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="dd9d8-172">ブラウザーを閉じて再び開いても状態を維持することがアプリに求められる場合、`localStorage` が最善の選択肢です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-172">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="dd9d8-173">`localStorage` および `sessionStorage` に格納されているデータは、ユーザーによって表示または改ざんされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-173">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="in-memory-state-container-service"></a><span data-ttu-id="dd9d8-174">メモリ内状態コンテナー サービス</span><span class="sxs-lookup"><span data-stu-id="dd9d8-174">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd9d8-175">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="dd9d8-175">Additional resources</span></span>

* [<span data-ttu-id="dd9d8-176">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-176">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="dd9d8-177">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-177">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="dd9d8-178">アプリでは、ほとんど常に、サーバーとの接続が維持されています。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-178">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="dd9d8-179">ユーザーの状態は、"*回線*" の中のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-179">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="dd9d8-180">たとえば、回線には次のようなユーザー状態が保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-180">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="dd9d8-181">レンダリングされた UI での、コンポーネント インスタンスの階層と最新のレンダリング出力。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-181">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="dd9d8-182">コンポーネント インスタンスに含まれるフィールドとプロパティの値。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-182">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="dd9d8-183">回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータ。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-183">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="dd9d8-184">ユーザー状態は、[JavaScript 相互運用](xref:blazor/call-javascript-from-dotnet)の呼び出しによって設定される、ブラウザーのメモリ内の JavaScript 変数にも存在する場合があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-184">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="dd9d8-185">ユーザーが一時的にネットワークに接続できなくなった場合は、Blazor により、ユーザーを元の状態で元の回線に再接続することが試みられます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-185">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="dd9d8-186">ただし、サーバーのメモリにある元の回線にいつでもユーザーを再接続できるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-186">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="dd9d8-187">サーバーでは、切断された回線を永久に保持することはできません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-187">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="dd9d8-188">サーバーでは、タイムアウト後、あるいはサーバーがメモリ不足になったとき、切断された回線を解放しなければなりません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-188">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="dd9d8-189">複数のサーバーが存在する負荷分散された展開環境では、個々のサーバーは、それがなくても全体的な要求量を処理できるようになると、作動しなくなったり、自動的に削除されたりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-189">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="dd9d8-190">ユーザーが再接続しようとしたときに、ユーザーの要求を処理していた元のサーバーを使用できなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-190">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="dd9d8-191">ユーザーはブラウザーを閉じて再び起動するか、ページを再読み込みできます。それでブラウザーのメモリに保存されている状態が削除されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-191">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="dd9d8-192">たとえば、JavaScript 相互運用の呼び出しによって設定された JavaScript 変数の値は失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-192">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="dd9d8-193">ユーザーが元の回線に再接続できないとき、そのユーザーには、状態が空の新しい回線が与えられます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-193">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="dd9d8-194">これはデスクトップ アプリを終了してもう一度起動することと同じです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-194">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="dd9d8-195">回線間で状態を維持する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-195">Persist state across circuits</span></span>

<span data-ttu-id="dd9d8-196">一般に、ユーザーが既存データを単に読み取るのではなく、活発にデータを作成している場合は、回線間で状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-196">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="dd9d8-197">回線間で状態を維持するには、アプリで、サーバーのメモリとは異なる他の保存場所に、データを保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-197">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="dd9d8-198">状態は自動的に保存されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-198">State persistence isn't automatic.</span></span> <span data-ttu-id="dd9d8-199">ステートフルなデータ保存を実装するアプリを開発するとき、措置を講じる必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-199">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="dd9d8-200">データの保持は、通常、作成するためにユーザーが大きな労力を費やした、価値の高い状態の場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-200">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="dd9d8-201">次の例では、状態を維持することで、時間が節約され、商業活動の支援になります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-201">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="dd9d8-202">複数ステップの Web フォーム: 複数ステップの Web フォームのいくつかのステップを完了した後で、状態が失われた場合、ユーザーがデータを再入力するのでは時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-202">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="dd9d8-203">このシナリオでは、フォームからユーザーが離れて後で戻ってきた場合、状態が失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-203">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="dd9d8-204">ショッピング カート: 収益につながる可能性がある、アプリの中のビジネス上重要なコンポーネントを維持できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-204">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="dd9d8-205">ユーザーが自分の状態を失い、そのため、自分のショッピング カートが消えると、後でサイトに戻ってきたとき、製品やサービスの購入数が減ることがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-205">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="dd9d8-206">アプリでは、"*アプリの状態*" のみが維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-206">An app can only persist *app state*.</span></span> <span data-ttu-id="dd9d8-207">コンポーネント インスタンスやそのレンダー ツリーなど、UI は維持されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-207">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="dd9d8-208">コンポーネントとレンダー ツリーは一般的にシリアル化されません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-208">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="dd9d8-209">アプリで、ツリー ビュー コントロールの展開されたノードなど、UI の状態を維持するには、カスタム コードを使用して、UI 状態の動作をシリアル化できるアプリ状態としてモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-209">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="dd9d8-210">状態を維持する場所</span><span class="sxs-lookup"><span data-stu-id="dd9d8-210">Where to persist state</span></span>

<span data-ttu-id="dd9d8-211">一般に、状態を維持するために、次のような場所があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-211">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="dd9d8-212">サーバー側ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-212">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="dd9d8-213">URL</span><span class="sxs-lookup"><span data-stu-id="dd9d8-213">URL</span></span>](#url)
* [<span data-ttu-id="dd9d8-214">ブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-214">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="dd9d8-215">メモリ内状態コンテナー サービス</span><span class="sxs-lookup"><span data-stu-id="dd9d8-215">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="dd9d8-216">サーバー側ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-216">Server-side storage</span></span>

<span data-ttu-id="dd9d8-217">複数のユーザーとデバイスにまたがって永続的にデータを保持する場合は、サーバー側ストレージをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-217">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="dd9d8-218">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-218">Options include:</span></span>

* <span data-ttu-id="dd9d8-219">BLOB ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-219">Blob storage</span></span>
* <span data-ttu-id="dd9d8-220">キーと値のストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-220">Key-value storage</span></span>
* <span data-ttu-id="dd9d8-221">リレーショナル データベース</span><span class="sxs-lookup"><span data-stu-id="dd9d8-221">Relational database</span></span>
* <span data-ttu-id="dd9d8-222">テーブル ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-222">Table storage</span></span>

<span data-ttu-id="dd9d8-223">データが保存された後は、ユーザーの状態は保持されていて、新しい回線で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-223">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="dd9d8-224">Azure のデータ ストレージ オプションの詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-224">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="dd9d8-225">Azure のデータベース</span><span class="sxs-lookup"><span data-stu-id="dd9d8-225">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="dd9d8-226">Azure Storage のドキュメント</span><span class="sxs-lookup"><span data-stu-id="dd9d8-226">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="dd9d8-227">URL</span><span class="sxs-lookup"><span data-stu-id="dd9d8-227">URL</span></span>

<span data-ttu-id="dd9d8-228">ナビゲーションの状態を表わす一時的なデータについては、URL の一部としてデータをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-228">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="dd9d8-229">たとえば、次のようなユーザー状態が URL でモデル化されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-229">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="dd9d8-230">表示されるエンティティの ID。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-230">The ID of a viewed entity.</span></span>
* <span data-ttu-id="dd9d8-231">ページ付きグリッドでの現在のページ番号。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-231">The current page number in a paged grid.</span></span>

<span data-ttu-id="dd9d8-232">次の場合、ブラウザーのアドレス バーのコンテンツが保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-232">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="dd9d8-233">ユーザーがページを手動で再読み込みした。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-233">If the user manually reloads the page.</span></span>
* <span data-ttu-id="dd9d8-234">Web サーバーが利用できなくなり、別のサーバーに接続する目的で、ページの再読み込みがユーザーに強制される。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-234">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="dd9d8-235">[`@page`](xref:mvc/views/razor#page) ディレクティブで URL パターンを定義する方法については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-235">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="dd9d8-236">ブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-236">Browser storage</span></span>

<span data-ttu-id="dd9d8-237">ユーザーが頻繁に作成する一時的なデータの場合、一般的に使用されるストレージの場所は、ブラウザーの [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) コレクションと [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) コレクションです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-237">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="dd9d8-238">`localStorage` の対象範囲はブラウザーのウィンドウです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-238">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="dd9d8-239">ユーザーがページを再読み込みするか、ブラウザーを閉じて再び開くと、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-239">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="dd9d8-240">ユーザーが複数のブラウザー タブを開くと、状態はすべてのタブで同じになります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-240">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="dd9d8-241">データは直接消去されるまで `localStorage` に残ります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-241">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="dd9d8-242">`sessionStorage` の対象範囲はブラウザーのタブです。ユーザーがタブを再読み込みすると、状態は維持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-242">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="dd9d8-243">ユーザーがタブかブラウザーを閉じると、状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-243">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="dd9d8-244">ユーザーが複数のブラウザー タブを開くと、それぞれのタブには、他に依存しないそのタブだけのバージョンのデータが保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-244">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="dd9d8-245">一般に、`sessionStorage` を使用しておけば安全です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-245">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="dd9d8-246">`sessionStorage` の場合、ユーザーが複数のタブを開き、以下に遭遇するリスクが回避されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-246">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="dd9d8-247">タブ間の状態保存に含まれるバグ。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-247">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="dd9d8-248">タブで他のタブの状態が上書きされるときの紛らわしい動作。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-248">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="dd9d8-249">ブラウザーを閉じて再び開いても状態を維持することがアプリに求められる場合、`localStorage` が最善の選択肢です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-249">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="dd9d8-250">ブラウザー ストレージ使用時の注意事項:</span><span class="sxs-lookup"><span data-stu-id="dd9d8-250">Caveats for using browser storage:</span></span>

* <span data-ttu-id="dd9d8-251">サーバー側データベースの使用に似ていますが、データの読み込みと保存は非同期です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-251">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="dd9d8-252">サーバー側データベースとは異なり、プリレンダリング中はストレージを利用できません。プリレンダリング中は、要求されたページがブラウザーに存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-252">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="dd9d8-253">Blazor Server アプリの場合、数キロバイトのデータをストレージに保持するのが妥当です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-253">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="dd9d8-254">数キロバイトを超えると、パフォーマンスに影響が出ることを考慮する必要があります。ネットワーク中でデータが読み込まれ、保存されるためです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-254">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="dd9d8-255">ユーザーはデータを見たり、改ざんしたりするかもしれません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-255">Users may view or tamper with the data.</span></span> <span data-ttu-id="dd9d8-256">[ASP.NET Core のデータ保護](xref:security/data-protection/introduction)で、このリスクを軽減できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-256">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="dd9d8-257">たとえば、[ASP.NET Core で保護されたブラウザー ストレージ](#aspnet-core-protected-browser-storage)では、ASP.NET Core のデータ保護が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-257">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="dd9d8-258">サードパーティ製 NuGet パッケージからは、`localStorage` と `sessionStorage` を使用するための API が与えられます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-258">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="dd9d8-259">[ASP.NET Core のデータ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択してみることもお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-259">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="dd9d8-260">データ保護を使用すると、保存データが暗号化され、保存データが改ざんされる潜在的リスクが減ります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-260">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="dd9d8-261">JSON でシリアル化されたデータがプレーンテキストで保存されている場合、ユーザーはブラウザー開発者ツールでデータを表示できます。また、保存データを変更できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-261">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="dd9d8-262">データが性質上、取るに足りないものであれば、データ セキュリティを問題にする必要はないかもしれません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-262">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="dd9d8-263">たとえば、UI 要素に保存されている色を読み取られたり、変更されたりしたところで、それはユーザーや組織にとって大きなセキュリティ リスクではありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-263">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="dd9d8-264">"*取り扱いに慎重を要するデータ*" を見たり、改ざんしたりすることをユーザーに禁止します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-264">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="dd9d8-265">ASP.NET Core で保護されたブラウザー ストレージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-265">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="dd9d8-266">ASP.NET Core で保護されたブラウザー ストレージでは、[`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) と [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) に対して [ASP.NET Core のデータ保護](xref:security/data-protection/introduction)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-266">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="dd9d8-267">保護されたブラウザー ストレージは、ASP.NET Core のデータ保護に依存しており、Blazor Server アプリでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-267">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="dd9d8-268">構成</span><span class="sxs-lookup"><span data-stu-id="dd9d8-268">Configuration</span></span>

1. <span data-ttu-id="dd9d8-269">[`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-269">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="dd9d8-270">`Startup.ConfigureServices` で `AddProtectedBrowserStorage` を呼び出し、`localStorage` サービスと `sessionStorage` サービスをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-270">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="dd9d8-271">コンポーネント内でデータを保存し、読み込む</span><span class="sxs-lookup"><span data-stu-id="dd9d8-271">Save and load data within a component</span></span>

<span data-ttu-id="dd9d8-272">ブラウザー ストレージのデータの読み込みまたは保存が必要なすべてのコンポーネントで、[`@inject`](xref:mvc/views/razor#inject) ディレクティブを使用して、次のいずれかのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-272">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="dd9d8-273">どれを選択するかは、使用するブラウザー ストレージの場所によって異なります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-273">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="dd9d8-274">次の例では、`sessionStorage` が使用されています。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-274">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="dd9d8-275">`@using` ディレクティブは、コンポーネントの代わりに、アプリの `_Imports.razor` ファイルに配置できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-275">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="dd9d8-276">`_Imports.razor` ファイルを使用すると、アプリの中の大きなセグメントで、あるいはアプリ全体で名前空間を利用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-276">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="dd9d8-277">Blazor Server プロジェクト テンプレートに基づいてアプリの `Counter` コンポーネントに `currentCount` の値を保持するには、`ProtectedSessionStore.SetAsync` を使用するように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-277">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="dd9d8-278">もっと大規模で現実に即したアプリの場合、個々のフィールドを保管するというのはありそうにないシナリオです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-278">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="dd9d8-279">アプリでは多くの場合、状態が複雑なモデル オブジェクト全体を保存します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-279">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="dd9d8-280">`ProtectedSessionStore` では、複雑な状態オブジェクトを格納するため、JSON データが自動的にシリアル化および逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-280">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="dd9d8-281">前のコード例では、`currentCount` データは、ユーザーのブラウザーに `sessionStorage['count']` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-281">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="dd9d8-282">データはプレーンテキストに保存されず、ASP.NET Core のデータ保護を使用して保護されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-282">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="dd9d8-283">ブラウザーの開発者コンソールで `sessionStorage['count']` が評価される場合、暗号化されたデータを調べることができます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-283">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="dd9d8-284">ユーザーが後で `Counter` コンポーネントに戻ったときに `currentCount` データを回復するには (ユーザーが新しい回線にいる場合も含め)、`ProtectedSessionStore.GetAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-284">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="dd9d8-285">コンポーネントのパラメーターにナビゲーションの状態が含まれている場合は、`ProtectedSessionStore.GetAsync` を呼び出して、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ではなく、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> に `null` 以外の結果を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-285">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="dd9d8-286"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> は、コンポーネントが最初にインスタンス化されたときに 1 回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-286"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="dd9d8-287">後で、その同じページにいるとき、ユーザーが別の URL に移動しても <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> が再び呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-287"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="dd9d8-288">詳細については、「<xref:blazor/components/lifecycle>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-288">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="dd9d8-289">このセクションの例は、サーバーでプリレンダリングが有効になっていない場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-289">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="dd9d8-290">プリレンダリングが有効になっていると、コンポーネントがプリレンダリングされているために JavaScript 相互運用の呼び出しを発行できないことを示すエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-290">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="dd9d8-291">プリレンダリングを無効にするか、プリレンダリングで使用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-291">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="dd9d8-292">プリレンダリングと連動するコードを記述する方法の詳細については、「[プリレンダリングを処理する](#handle-prerendering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-292">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="dd9d8-293">読み込み状態を処理する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-293">Handle the loading state</span></span>

<span data-ttu-id="dd9d8-294">ブラウザー ストレージはネットワーク接続経由で非同期にアクセスされるため、データが読み込まれ、コンポーネントで利用できるようになるまでに、常に一定の時間があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-294">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="dd9d8-295">最良の結果を得るには、空のデータや既定のデータを表示するのではなく、読み込みが進行中のとき、読み込み状態メッセージをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-295">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="dd9d8-296">1 つの手法は、データが `null` かどうかを追跡することです。これは、まだ読み込み中であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-296">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="dd9d8-297">既定の `Counter` コンポーネントでは、カウントは `int` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-297">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="dd9d8-298">型 (`int`) に疑問符 (`?`) を追加することで、[`currentCount` を Null 許容にします](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-298">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="dd9d8-299">カウントや **`Increment`** ボタンを無条件で表示するのではなく、<xref:System.Nullable%601.HasValue%2A> を調べることで、データが読み込まれている場合にのみこれらの要素を表示します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-299">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="dd9d8-300">プリレンダリングを処理する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-300">Handle prerendering</span></span>

<span data-ttu-id="dd9d8-301">プリレンダリング中:</span><span class="sxs-lookup"><span data-stu-id="dd9d8-301">During prerendering:</span></span>

* <span data-ttu-id="dd9d8-302">ユーザーのブラウザーに対話式で接続することはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-302">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="dd9d8-303">ブラウザーには、JavaScript コードを実行できるページがまだありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-303">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="dd9d8-304">`localStorage` または `sessionStorage` は、プリレンダリング中に使用できません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-304">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="dd9d8-305">コンポーネントがストレージとのやり取りを試みている場合、コンポーネントがプリレンダリングされているために JavaScript 相互運用の呼び出しを発行できないことを示すエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-305">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="dd9d8-306">このエラーを解決する方法の 1 つは、プリレンダリングを無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-306">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="dd9d8-307">これは通常、ブラウザーベースのストレージがアプリで頻繁に使用される場合、最良の選択肢となります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-307">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="dd9d8-308">プリレンダリングによってさらに複雑になり、アプリにとっては良いことがありません。アプリでは `localStorage` または `sessionStorage` が利用できなければ、役に立つコンテンツをプリレンダリングできないからです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-308">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="dd9d8-309">プリレンダリングを無効にするには、`Pages/_Host.cshtml` ファイルを開き、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)の `render-mode` 属性を、<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> に変更します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-309">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="dd9d8-310">プリレンダリングは、`localStorage` や `sessionStorage` を使用しない他のページでは役に立つかもしれません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-310">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="dd9d8-311">プリレンダリングを保持するには、ブラウザーが回線に接続されるまで読み込み操作を延期します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-311">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="dd9d8-312">次はカウンター値を格納する例です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-312">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="dd9d8-313">状態保存を取り出し、共通の場所に入れる</span><span class="sxs-lookup"><span data-stu-id="dd9d8-313">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="dd9d8-314">さまざまなコンポーネントがブラウザーベースのストレージに依存しているとき、状態プロバイダー コードを何回も再実装すると、コードが重複します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-314">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="dd9d8-315">コードの重複を回避する選択肢の 1 つは、状態プロバイダー ロジックをカプセル化する "*状態プロバイダーの親コンポーネント*" を作成することです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-315">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="dd9d8-316">状態保存メカニズムに関係なく、子コンポーネントは永続保存データとやりとりできます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-316">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="dd9d8-317">`CounterStateProvider` コンポーネントの次の例では、カウンター データは `sessionStorage` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-317">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="dd9d8-318">`CounterStateProvider` コンポーネントによって読み込み段階が処理されます。読み込みが完了するまで、その子コンテンツがレンダリングされることはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-318">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="dd9d8-319">`CounterStateProvider` コンポーネントを使用するには、カウンター状態にアクセスする必要がある他のコンポーネントをコンポーネントのインスタンスでラップします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-319">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="dd9d8-320">アプリに含まれるすべてのコンポーネントが状態にアクセスできるようにするには、`App` コンポーネント (`App.razor`) で <xref:Microsoft.AspNetCore.Components.Routing.Router> を `CounterStateProvider` コンポーネントでラップします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-320">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="dd9d8-321">ラップされたコンポーネントの元に永続化されたカウンター状態が届くので、それを変更できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-321">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="dd9d8-322">次の `Counter` コンポーネントはパターンが実装されています。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-322">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="dd9d8-323">このコンポーネントは `ProtectedBrowserStorage` とやりとりするために必要ありませんし、"読み込み" 段階にも関係ありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-323">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="dd9d8-324">前に説明したようにプリレンダリングを扱うには、カウンター データを利用するすべてのコンポーネントが自動的にプリレンダリングを使用するよう、`CounterStateProvider` を変更できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-324">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="dd9d8-325">詳細については、「[プリレンダリングを処理する](#handle-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-325">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="dd9d8-326">一般に、次の場合、"*状態プロバイダーの親コンポーネント*" パターンが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-326">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="dd9d8-327">多くのコンポーネントで状態を使用する。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-327">To consume state across many components.</span></span>
* <span data-ttu-id="dd9d8-328">最上位の状態オブジェクトを 1 つだけ保持する場合。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-328">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="dd9d8-329">さまざまな状態オブジェクトを保持し、さまざまな場所でさまざまなオブジェクト サブセットを使用するには、状態をグローバルに保持しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-329">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="dd9d8-330">Protected Browser Storage 試験用 NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="dd9d8-330">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="dd9d8-331">ASP.NET Core で保護されたブラウザー ストレージでは、[`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) と [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) に対して [ASP.NET Core のデータ保護](xref:security/data-protection/introduction)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-331">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="dd9d8-332">`Microsoft.AspNetCore.ProtectedBrowserStorage` はサポートのない試験用パッケージであり、運用環境での使用に適していません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-332">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="dd9d8-333">パッケージは、ASP.NET Core 3.1 の Blazor Server アプリでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-333">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="dd9d8-334">構成</span><span class="sxs-lookup"><span data-stu-id="dd9d8-334">Configuration</span></span>

1. <span data-ttu-id="dd9d8-335">[`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-335">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="dd9d8-336">`Pages/_Host.cshtml` ファイルで、終了 `</body>` タグの内部に、次のスクリプトを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-336">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="dd9d8-337">`Startup.ConfigureServices` で `AddProtectedBrowserStorage` を呼び出し、`localStorage` サービスと `sessionStorage` サービスをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-337">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="dd9d8-338">コンポーネント内でデータを保存し、読み込む</span><span class="sxs-lookup"><span data-stu-id="dd9d8-338">Save and load data within a component</span></span>

<span data-ttu-id="dd9d8-339">ブラウザー ストレージのデータの読み込みまたは保存が必要なすべてのコンポーネントで、[`@inject`](xref:mvc/views/razor#inject) ディレクティブを使用して、次のいずれかのインスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-339">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="dd9d8-340">どれを選択するかは、使用するブラウザー ストレージの場所によって異なります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-340">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="dd9d8-341">次の例では、`sessionStorage` が使用されています。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-341">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="dd9d8-342">`@using` ステートメントは、コンポーネントの代わりに、`_Imports.razor` ファイルに入れることができます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-342">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="dd9d8-343">`_Imports.razor` ファイルを使用すると、アプリの中の大きなセグメントで、あるいはアプリ全体で名前空間を利用できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-343">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="dd9d8-344">Blazor Server プロジェクト テンプレートに基づいてアプリの `Counter` コンポーネントに `currentCount` の値を保持するには、`ProtectedSessionStore.SetAsync` を使用するように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-344">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="dd9d8-345">もっと大規模で現実に即したアプリの場合、個々のフィールドを保管するというのはありそうにないシナリオです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-345">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="dd9d8-346">アプリでは多くの場合、状態が複雑なモデル オブジェクト全体を保存します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-346">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="dd9d8-347">`ProtectedSessionStore` では、JSON データが自動的にシリアル化され、逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-347">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="dd9d8-348">前のコード例では、`currentCount` データは、ユーザーのブラウザーに `sessionStorage['count']` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-348">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="dd9d8-349">データはプレーンテキストに保存されず、ASP.NET Core のデータ保護を使用して保護されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-349">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="dd9d8-350">ブラウザーの開発者コンソールで `sessionStorage['count']` が評価される場合、暗号化されたデータを調べることができます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-350">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="dd9d8-351">ユーザーが後で `Counter` コンポーネントに戻ったときに `currentCount` データを回復するには (まったく新しい回線にいる場合も含め)、`ProtectedSessionStore.GetAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-351">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="dd9d8-352">コンポーネントのパラメーターにナビゲーションの状態が含まれている場合、`ProtectedSessionStore.GetAsync` を呼び出し、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ではなく、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> で結果を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-352">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="dd9d8-353"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> は、コンポーネントが最初にインスタンス化されたときに 1 回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-353"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="dd9d8-354">後で、その同じページにいるとき、ユーザーが別の URL に移動しても <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> が再び呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-354"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="dd9d8-355">詳細については、「<xref:blazor/components/lifecycle>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-355">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="dd9d8-356">このセクションの例は、サーバーでプリレンダリングが有効になっていない場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-356">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="dd9d8-357">プリレンダリングが有効になっていると、コンポーネントがプリレンダリングされているために JavaScript 相互運用の呼び出しを発行できないことを示すエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-357">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="dd9d8-358">プリレンダリングを無効にするか、プリレンダリングで使用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-358">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="dd9d8-359">プリレンダリングと連動するコードを記述する方法の詳細については、「[プリレンダリングを処理する](#handle-prerendering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-359">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="dd9d8-360">読み込み状態を処理する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-360">Handle the loading state</span></span>

<span data-ttu-id="dd9d8-361">ブラウザー ストレージはネットワーク接続経由で非同期にアクセスされるため、データが読み込まれ、コンポーネントで利用できるようになるまでに、常に一定の時間があります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-361">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="dd9d8-362">最良の結果を得るには、空のデータや既定のデータを表示するのではなく、読み込みが進行中のとき、読み込み状態メッセージをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-362">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="dd9d8-363">1 つの手法は、データが `null` かどうかを追跡することです。これは、まだ読み込み中であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-363">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="dd9d8-364">既定の `Counter` コンポーネントでは、カウントは `int` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-364">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="dd9d8-365">型 (`int`) に疑問符 (`?`) を追加することで、[`currentCount` を Null 許容にします](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-365">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="dd9d8-366">カウントや **`Increment`** ボタンを無条件で表示するのではなく、データが読み込まれる場合にのみこれらの要素を表示するように選択します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-366">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="dd9d8-367">プリレンダリングを処理する</span><span class="sxs-lookup"><span data-stu-id="dd9d8-367">Handle prerendering</span></span>

<span data-ttu-id="dd9d8-368">プリレンダリング中:</span><span class="sxs-lookup"><span data-stu-id="dd9d8-368">During prerendering:</span></span>

* <span data-ttu-id="dd9d8-369">ユーザーのブラウザーに対話式で接続することはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-369">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="dd9d8-370">ブラウザーには、JavaScript コードを実行できるページがまだありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-370">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="dd9d8-371">`localStorage` または `sessionStorage` は、プリレンダリング中に使用できません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-371">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="dd9d8-372">コンポーネントがストレージとのやり取りを試みている場合、コンポーネントがプリレンダリングされているために JavaScript 相互運用の呼び出しを発行できないことを示すエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-372">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="dd9d8-373">このエラーを解決する方法の 1 つは、プリレンダリングを無効にすることです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-373">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="dd9d8-374">これは通常、ブラウザーベースのストレージがアプリで頻繁に使用される場合、最良の選択肢となります。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-374">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="dd9d8-375">プリレンダリングによってさらに複雑になり、アプリにとっては良いことがありません。アプリでは `localStorage` または `sessionStorage` が利用できなければ、役に立つコンテンツをプリレンダリングできないからです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-375">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="dd9d8-376">プリレンダリングを無効にするには、`Pages/_Host.cshtml` ファイルを開き、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)の `render-mode` 属性を、<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> に変更します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-376">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="dd9d8-377">プリレンダリングは、`localStorage` や `sessionStorage` を使用しない他のページでは役に立つかもしれません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-377">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="dd9d8-378">プリレンダリングを保持するには、ブラウザーが回線に接続されるまで読み込み操作を延期します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-378">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="dd9d8-379">次はカウンター値を格納する例です。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-379">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="dd9d8-380">状態保存を取り出し、共通の場所に入れる</span><span class="sxs-lookup"><span data-stu-id="dd9d8-380">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="dd9d8-381">さまざまなコンポーネントがブラウザーベースのストレージに依存しているとき、状態プロバイダー コードを何回も再実装すると、コードが重複します。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-381">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="dd9d8-382">コードの重複を回避する選択肢の 1 つは、状態プロバイダー ロジックをカプセル化する "*状態プロバイダーの親コンポーネント*" を作成することです。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-382">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="dd9d8-383">状態保存メカニズムに関係なく、子コンポーネントは永続保存データとやりとりできます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-383">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="dd9d8-384">`CounterStateProvider` コンポーネントの次の例では、カウンター データは `sessionStorage` に保持されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-384">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="dd9d8-385">`CounterStateProvider` コンポーネントによって読み込み段階が処理されます。読み込みが完了するまで、その子コンテンツがレンダリングされることはありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-385">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="dd9d8-386">`CounterStateProvider` コンポーネントを使用するには、カウンター状態にアクセスする必要がある他のコンポーネントをコンポーネントのインスタンスでラップします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-386">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="dd9d8-387">アプリに含まれるすべてのコンポーネントが状態にアクセスできるようにするには、`App` コンポーネント (`App.razor`) で <xref:Microsoft.AspNetCore.Components.Routing.Router> を `CounterStateProvider` コンポーネントでラップします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-387">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="dd9d8-388">ラップされたコンポーネントの元に永続化されたカウンター状態が届くので、それを変更できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-388">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="dd9d8-389">次の `Counter` コンポーネントはパターンが実装されています。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-389">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="dd9d8-390">このコンポーネントは `ProtectedBrowserStorage` とやりとりするために必要ありませんし、"読み込み" 段階にも関係ありません。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-390">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="dd9d8-391">前に説明したようにプリレンダリングを扱うには、カウンター データを利用するすべてのコンポーネントが自動的にプリレンダリングを使用するよう、`CounterStateProvider` を変更できます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-391">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="dd9d8-392">詳細については、「[プリレンダリングを処理する](#handle-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-392">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="dd9d8-393">一般的に、次の場合、"*状態プロバイダーの親コンポーネント*" パターンが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-393">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="dd9d8-394">多くのコンポーネントで状態を使用する。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-394">To consume state across many components.</span></span>
* <span data-ttu-id="dd9d8-395">最上位の状態オブジェクトを 1 つだけ保持する場合。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-395">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="dd9d8-396">さまざまな状態オブジェクトを保持し、さまざまな場所でさまざまなオブジェクト サブセットを使用するには、状態をグローバルに保持しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd9d8-396">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

## <a name="in-memory-state-container-service"></a><span data-ttu-id="dd9d8-397">メモリ内状態コンテナー サービス</span><span class="sxs-lookup"><span data-stu-id="dd9d8-397">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
