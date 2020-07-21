---
title: Blazor Todo リスト アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176232"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="ee043-103">Blazor Todo リスト アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="ee043-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="ee043-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ee043-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ee043-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ee043-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="ee043-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ee043-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ee043-107">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ee043-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ee043-108">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="ee043-108">Modify Razor components</span></span>
> * <span data-ttu-id="ee043-109">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="ee043-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ee043-110">Blazor アプリで依存関係の挿入 (DI) とルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="ee043-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ee043-111">このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="ee043-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="ee043-112">コマンド シェルで `TodoList` という名前の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="ee043-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="ee043-113">上記のコマンドでは、アプリを保持するための `TodoList` という名前のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ee043-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="ee043-114">次のコマンドを使用して、ディレクトリを `TodoList` フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ee043-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="ee043-115">次のコマンドを使用して、新しい `Todo` Razor コンポーネントを、`Pages` フォルダー内のアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="ee043-116"> コンポーネント ファイル名の先頭文字を大文字にする必要があります。そのため、`Todo` コンポーネント ファイルの名前が大文字の `T` で始まることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee043-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="ee043-117">`Pages/Todo.razor` で、コンポーネントの最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="ee043-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="ee043-118">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ee043-119">`NavMenu` コンポーネント (`Shared/NavMenu.razor`) はアプリのレイアウトで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ee043-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="ee043-120">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="ee043-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="ee043-121">`Shared/NavMenu.razor` ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加し、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="ee043-122">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="ee043-122">Rebuild and run the app.</span></span> <span data-ttu-id="ee043-123">新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee043-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="ee043-124">Todo アイテムを表すクラスを保持するために、プロジェクトのルートに `TodoItem.cs` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="ee043-125">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="ee043-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="ee043-126">`Todo` コンポーネント (`Pages/Todo.razor`) に戻ります。</span><span class="sxs-lookup"><span data-stu-id="ee043-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="ee043-127">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="ee043-128">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="ee043-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ee043-129">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="ee043-130">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="ee043-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ee043-131">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="ee043-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="ee043-132">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="ee043-132">Rebuild and run the app.</span></span> <span data-ttu-id="ee043-133">**`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="ee043-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="ee043-134">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="ee043-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="ee043-135">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ee043-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="ee043-136">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="ee043-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="ee043-137">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="ee043-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ee043-138">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="ee043-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="ee043-139">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="ee043-139">Rebuild and run the app.</span></span> <span data-ttu-id="ee043-140">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="ee043-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="ee043-141">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ee043-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ee043-142">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="ee043-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ee043-143">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="ee043-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="ee043-144">それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="ee043-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="ee043-145">完成した `Todo` コンポーネント (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="ee043-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="ee043-146">アプリケーションをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="ee043-146">Rebuild and run the app.</span></span> <span data-ttu-id="ee043-147">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="ee043-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ee043-148">次の手順</span><span class="sxs-lookup"><span data-stu-id="ee043-148">Next steps</span></span>

<span data-ttu-id="ee043-149">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="ee043-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ee043-150">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ee043-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ee043-151">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="ee043-151">Modify Razor components</span></span>
> * <span data-ttu-id="ee043-152">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="ee043-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ee043-153">Blazor アプリで依存関係の挿入 (DI) とルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="ee043-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ee043-154">ASP.NET Core Blazor 用のツールについて学習します。</span><span class="sxs-lookup"><span data-stu-id="ee043-154">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
