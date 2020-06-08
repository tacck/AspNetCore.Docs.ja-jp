---
title: "title:'初めての Blazor アプリの作成' author: guardrex description:'Blazor アプリを段階的に構築します。'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.0' ms.author: riande ms.custom: mvc ms.date:05/19/2020 no-loc:"
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
ms.openlocfilehash: 8cf356b5c9876a4114942af27a22df6bd324d0e2
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355215"
---
# <a name="build-your-first-blazor-app"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

> [!div class="checklist"]
> * 'SignalR' uid: tutorials/first-blazor-app
> * 最初の Blazor アプリをビルドする
> * 作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)
> * このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。

以下の方法について説明します。

## <a name="build-components"></a>Todo リストの Blazor アプリ プロジェクトを作成する

1. Razor コンポーネントを変更する コンポーネントでイベント処理とデータ バインディングを使用する

1. Blazor アプリで依存関係の挿入 (DI) とルーティングを使用する このチュートリアルの最後には、動作するチャット アプリが完成します。

1. コンポーネントを構築する <xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。 プロジェクトに *ToDoList* という名前を付けます。

1. *Pages* フォルダー内にあるアプリの 3 つのページそれぞれを参照します。ホーム、カウンター、データのフェッチです。

   これらのページは Razor コンポーネント ファイル *Index.razor*、*Counter.razor*、*FetchData.razor* によって実装されています。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。 Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要です。 Blazor を使用すると、代わりに C# を記述できます。 *Counter.razor* ファイルで `Counter` コンポーネントの実装を調べます。

   *Pages/Counter.razor*: `Counter` コンポーネントの UI は、HTML を使って定義されています。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。

   HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。

   * 生成される .NET クラスの名前はファイル名と同じです。
   * コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。
   * `@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。
   * これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。 **[クリックしてください]** ボタンを選択すると:

1. `Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. `Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。 新しいレンダリング ツリーが以前のものと比較されます。 ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。

## <a name="use-components"></a>表示されている数が更新されます。

`Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。

1. アプリをリビルドして実行し、変更を確認します。

   **[クリックしてください]** ボタンを選択します。 カウンターは、2 ずつインクリメントされます。 コンポーネントを使う

   HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. `Index` コンポーネント (*Index.razor*) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。 このエクスペリエンスのために Blazor WebAssembly を使用している場合、`SurveyPrompt` コンポーネントによって `Index` コンポーネントが使用されます。

## <a name="component-parameters"></a>`<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。

このエクスペリエンスに Blazor サーバー アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。 *Pages/Index.razor*: アプリケーションをリビルドして実行します。

1. `Index` コンポーネントには、固有のカウンターがあります。

   * コンポーネントのパラメーター
   * コンポーネントにパラメーターを持たせることもできます。

   コンポーネントのパラメーターは、[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 属性が指定されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. マークアップ内でコンポーネントの引数を指定するには、属性を使います。 コンポーネントの `@code` C# コードを次のように更新します。

   [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 属性を使用してパブリック `IncrementAmount` プロパティを追加します。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. `currentCount` の値を増やすときに `IncrementAmount` プロパティを使うように `IncrementCount` メソッドを変更します。 *Pages/Counter.razor*: 属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。

## <a name="route-to-components"></a>カウンターを 10 ずつインクリメントするように値を設定します。

*Pages/Index.razor*: `Index` コンポーネントを再度読み込みます。 カウンターは、 **[クリックしてください]** ボタンを選択するたびに 10 ずつインクリメントされます。

## <a name="dependency-injection"></a>`Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。

### <a name="blazor-server-experience"></a>コンポーネントにルーティングする

*Counter.razor* ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。 `Counter` コンポーネントによって `/counter` に送信された要求が処理されます。

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

`@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。

依存関係の挿入

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Blazor サーバーのエクスペリエンス

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor サーバー アプリを使用している場合、`WeatherForecastService` サービスは `Startup.ConfigureServices` の[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されます。

サービスのインスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してアプリ全体で利用できます。

`FetchData` コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、[`@inject`](xref:mvc/views/razor#inject) ディレクティブが使われています。

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。

Blazor WebAssembly のエクスペリエンス

1. Blazor WebAssembly アプリを使用する場合、*wwwroot/sample-data* フォルダー内の *weather.json* ファイルから天気予報データを取得するために、<xref:System.Net.Http.HttpClient> が挿入されます。 *Pages/FetchData.razor*: 各 forecast 変数を気象データのテーブルの行としてレンダリングするために、[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。 Todo リストを構築する

1. シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. 新しい `Todo` Razor コンポーネントを、*Pages* フォルダー内のアプリに追加します。

   Visual Studio を使用している場合、**Pages** フォルダーを右クリックし、 **[追加]**  >  **[新しい項目]**  >  **[Razor コンポーネント]** の順に選択します。 コンポーネントのファイルに "*Todo.razor*" という名前を指定します。

   その他の開発環境では、"*Todo.razor*" という名前の **Pages** フォルダーに空のファイルを追加します。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. コンポーネントに最初のマークアップを指定します。 ナビゲーション バーに `Todo` コンポーネントを追加します。

1. アプリのレイアウトでは、`NavMenu` コンポーネント (*Shared/NavMenu.csthml*) が使用されます。 レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. *Shared/NavMenu.csthml* ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加して、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。

   * アプリケーションをリビルドして実行します。 新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。
   * Todo アイテムを表すクラスを保持するために、プロジェクトのルートに *TodoItem.cs* ファイルを追加します。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. `TodoItem` クラス用に次の C# コードを使います。 `Todo` コンポーネントに戻ります (*Pages/Todo.razor*)。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Todo 項目用のフィールドを `@code` ブロックに追加します。 `Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。

1. 各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。 アプリには、リストに Todo 項目を追加するための UI 要素が必要です。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. 順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. アプリケーションをリビルドして実行します。 **[Add todo]\(ToDo の追加\)** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. `Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。 ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。

1. 新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。 指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。 `newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. アプリケーションをリビルドして実行します。

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. 各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。 各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。

## <a name="next-steps"></a>`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。

それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。

> [!div class="checklist"]
> * 完成した `Todo` コンポーネント (*Pages/Todo.razor*):
> * アプリケーションをリビルドして実行します。
> * Todo アイテムを追加して新しいコードをテストします。
> * 次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="nextstepaction"]
> <xref:blazor/components>
