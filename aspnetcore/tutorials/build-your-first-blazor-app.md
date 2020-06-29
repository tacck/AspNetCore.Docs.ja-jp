---
title: 最初の Blazor アプリをビルドする
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292777"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="2b9dd-103">最初の Blazor アプリをビルドする</span><span class="sxs-lookup"><span data-stu-id="2b9dd-103">Build your first Blazor app</span></span>

<span data-ttu-id="2b9dd-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b9dd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2b9dd-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="2b9dd-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b9dd-107">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="2b9dd-108">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-108">Modify Razor components</span></span>
> * <span data-ttu-id="2b9dd-109">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="2b9dd-110">Blazor アプリで依存関係の挿入 (DI) とルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="2b9dd-111">このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="2b9dd-112">コンポーネントを構築する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-112">Build components</span></span>

1. <span data-ttu-id="2b9dd-113"><xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="2b9dd-114">プロジェクトに `ToDoList` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="2b9dd-115">`Pages` フォルダー内にあるアプリの 3 つのページ `Home`、`Counter`、`Fetch data` それぞれを閲覧します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="2b9dd-116">3 つのページは、Razor コンポーネント ファイル `Index.razor`、`Counter.razor`、`FetchData.razor` で実装されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="2b9dd-117">`Counter` ページで、ページを更新することなくカウンターをインクリメントするボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2b9dd-118">Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要です。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="2b9dd-119">Blazor を使用すると、代わりに C# を記述できます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="2b9dd-120">`Counter.razor` ファイルで `Counter` コンポーネントの実装を調べます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="2b9dd-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="2b9dd-122">`Counter` コンポーネントの UI は、HTML を使って定義されています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="2b9dd-123">動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="2b9dd-124">HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="2b9dd-125">生成される .NET クラスの名前はファイル名と同じです。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="2b9dd-126">コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="2b9dd-127">`@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="2b9dd-128">これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="2b9dd-129">カウンター インクリメント ボタンが選択されているとき:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="2b9dd-130">`Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="2b9dd-131">`Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="2b9dd-132">新しいレンダリング ツリーが以前のものと比較されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="2b9dd-133">ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="2b9dd-134">表示されている数が更新されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="2b9dd-135">`Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="2b9dd-136">アプリをリビルドして実行し、変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="2b9dd-137">ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-137">Select the button.</span></span> <span data-ttu-id="2b9dd-138">カウンターは、2 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="2b9dd-139">コンポーネントを使う</span><span class="sxs-lookup"><span data-stu-id="2b9dd-139">Use components</span></span>

<span data-ttu-id="2b9dd-140">HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="2b9dd-141">`Index` コンポーネント (`Index.razor`) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="2b9dd-142">このエクスペリエンスのために Blazor WebAssembly を使用している場合、`SurveyPrompt` コンポーネントによって `Index` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="2b9dd-143">`<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="2b9dd-144">このエクスペリエンスに Blazor サーバー アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="2b9dd-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="2b9dd-146">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-146">Rebuild and run the app.</span></span> <span data-ttu-id="2b9dd-147">`Index` コンポーネントには、固有のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="2b9dd-148">コンポーネントのパラメーター</span><span class="sxs-lookup"><span data-stu-id="2b9dd-148">Component parameters</span></span>

<span data-ttu-id="2b9dd-149">コンポーネントにパラメーターを持たせることもできます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-149">Components can also have parameters.</span></span> <span data-ttu-id="2b9dd-150">コンポーネントのパラメーターは、[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 属性が指定されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="2b9dd-151">マークアップ内でコンポーネントの引数を指定するには、属性を使います。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="2b9dd-152">コンポーネントの `@code` C# コードを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="2b9dd-153">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 属性を使用してパブリック `IncrementAmount` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="2b9dd-154">`currentCount` の値を増やすときに `IncrementAmount` プロパティを使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="2b9dd-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="2b9dd-156">属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="2b9dd-157">カウンターを 10 ずつインクリメントするように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="2b9dd-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="2b9dd-159">`Index` コンポーネントを再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-159">Reload the `Index` component.</span></span> <span data-ttu-id="2b9dd-160">カウンターは、ボタンを選択するたびに 10 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="2b9dd-161">`Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="2b9dd-162">コンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="2b9dd-162">Route to components</span></span>

<span data-ttu-id="2b9dd-163">`Counter.razor` ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="2b9dd-164">`Counter` コンポーネントによって `/counter` に送信された要求が処理されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="2b9dd-165">`@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="2b9dd-166">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="2b9dd-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="2b9dd-167"> サーバーのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="2b9dd-167"> Server experience</span></span>

<span data-ttu-id="2b9dd-168">Blazor サーバー アプリを使用している場合、`WeatherForecastService` サービスは `Startup.ConfigureServices` の[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2b9dd-169">サービスのインスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してアプリ全体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="2b9dd-170">`FetchData` コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、[`@inject`](xref:mvc/views/razor#inject) ディレクティブが使われています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="2b9dd-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="2b9dd-172">`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="2b9dd-173"> WebAssembly のエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="2b9dd-173"> WebAssembly experience</span></span>

<span data-ttu-id="2b9dd-174">Blazor WebAssembly アプリを使用する場合、`wwwroot/sample-data` フォルダー内の `weather.json` ファイルから天気予報データを取得するために、<xref:System.Net.Http.HttpClient> が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="2b9dd-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b9dd-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="2b9dd-176">各 forecast 変数を気象データのテーブルの行としてレンダリングするために、[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="2b9dd-177">Todo リストを構築する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-177">Build a todo list</span></span>

<span data-ttu-id="2b9dd-178">シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="2b9dd-179">新しい `Todo` Razor コンポーネントを、`Pages` フォルダー内のアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="2b9dd-180">Visual Studio を使用している場合、`Pages` フォルダーを右クリックし、 **[追加]** 、 **[新しい項目]** 、 **[Razor コンポーネント]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="2b9dd-181">コンポーネントのファイルに `Todo.razor` という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="2b9dd-182">その他の開発環境では、`Todo.razor` という名前の `Pages` フォルダーに空のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="2b9dd-183"> コンポーネント ファイル名の先頭文字を大文字にする必要があります。そのため、`Todo` コンポーネント ファイルの名前が大文字の `T` で始まることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="2b9dd-184">コンポーネントに最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="2b9dd-185">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="2b9dd-186">`NavMenu` コンポーネント (`Shared/NavMenu.razor`) はアプリのレイアウトで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="2b9dd-187">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="2b9dd-188">`Shared/NavMenu.razor` ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加し、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="2b9dd-189">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-189">Rebuild and run the app.</span></span> <span data-ttu-id="2b9dd-190">新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="2b9dd-191">Todo アイテムを表すクラスを保持するために、プロジェクトのルートに `TodoItem.cs` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="2b9dd-192">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="2b9dd-193">`Todo` コンポーネント (`Pages/Todo.razor`) に戻ります。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="2b9dd-194">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="2b9dd-195">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="2b9dd-196">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="2b9dd-197">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="2b9dd-198">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="2b9dd-199">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-199">Rebuild and run the app.</span></span> <span data-ttu-id="2b9dd-200">**`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="2b9dd-201">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="2b9dd-202">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="2b9dd-203">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="2b9dd-204">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="2b9dd-205">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="2b9dd-206">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-206">Rebuild and run the app.</span></span> <span data-ttu-id="2b9dd-207">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="2b9dd-208">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="2b9dd-209">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="2b9dd-210">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="2b9dd-211">それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="2b9dd-212">完成した `Todo` コンポーネント (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="2b9dd-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="2b9dd-213">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-213">Rebuild and run the app.</span></span> <span data-ttu-id="2b9dd-214">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2b9dd-215">次の手順</span><span class="sxs-lookup"><span data-stu-id="2b9dd-215">Next steps</span></span>

<span data-ttu-id="2b9dd-216">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b9dd-217">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="2b9dd-218">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-218">Modify Razor components</span></span>
> * <span data-ttu-id="2b9dd-219">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="2b9dd-220">Blazor アプリで依存関係の挿入 (DI) とルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="2b9dd-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="2b9dd-221">コンポーネントを構築して使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2b9dd-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
