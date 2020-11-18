---
title: Blazor Todo リスト アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- appsettings.json
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 4d5bd977b52dd20ffb62519720106ef0a4560914
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508137"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="34512-103">Blazor Todo リスト アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="34512-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="34512-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="34512-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="34512-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="34512-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="34512-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="34512-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34512-107">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="34512-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="34512-108">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="34512-108">Modify Razor components</span></span>
> * <span data-ttu-id="34512-109">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="34512-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="34512-110">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="34512-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="34512-111">このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="34512-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34512-112">前提条件</span><span class="sxs-lookup"><span data-stu-id="34512-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="34512-113">ToDo リスト Blazor アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="34512-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="34512-114">コマンド シェルで `TodoList` という名前の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="34512-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="34512-115">上記のコマンドでは、アプリを保持するための `TodoList` という名前のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="34512-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="34512-116">`TodoList` フォルダーは、プロジェクトの *ルート フォルダー* です。</span><span class="sxs-lookup"><span data-stu-id="34512-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="34512-117">次のコマンドを使用して、ディレクトリを `TodoList` フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="34512-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="34512-118">次のコマンドを使用して、新しい `Todo` Razor コンポーネントを、`Pages` フォルダー内のアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="34512-119">Razor コンポーネント ファイル名の先頭文字は、大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="34512-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="34512-120">`Pages` フォルダーを開き、`Todo` コンポーネントのファイル名の先頭が大文字 `T` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="34512-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="34512-121">ファイル名は `Todo.razor` のはずです。</span><span class="sxs-lookup"><span data-stu-id="34512-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="34512-122">`Pages/Todo.razor` で、コンポーネントの最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="34512-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="34512-123">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="34512-124">`NavMenu` コンポーネント (`Shared/NavMenu.razor`) はアプリのレイアウトで使用されます。</span><span class="sxs-lookup"><span data-stu-id="34512-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="34512-125">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="34512-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="34512-126">`Shared/NavMenu.razor` ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加し、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="34512-127">`TodoList` フォルダーからコマンド シェルで `dotnet run` コマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="34512-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="34512-128">`https://localhost:5001/todo` で新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="34512-128">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="34512-129">Todo アイテムを表すクラスを保持するために、プロジェクト (`TodoList` フォルダー) のルートに `TodoItem.cs` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="34512-130">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="34512-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="34512-131">`Todo` コンポーネント (`Pages/Todo.razor`) に戻ります。</span><span class="sxs-lookup"><span data-stu-id="34512-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="34512-132">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="34512-133">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="34512-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="34512-134">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="34512-135">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="34512-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="34512-136">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="34512-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="34512-137">コマンド シェルで実行中のアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="34512-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="34512-138">多くのコマンド シェルでは、<kbd>Ctrl</kbd>+<kbd>c</kbd> キーボード コマンドを使用してアプリを停止できます。</span><span class="sxs-lookup"><span data-stu-id="34512-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="34512-139">`dotnet run` コマンドを使用して、アプリをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="34512-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="34512-140">**`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。</span><span class="sxs-lookup"><span data-stu-id="34512-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="34512-141">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="34512-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="34512-142">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="34512-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="34512-143">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="34512-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="34512-144">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="34512-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="34512-145">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="34512-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="34512-146">コマンド シェルで実行中のアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="34512-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="34512-147">`dotnet run` コマンドを使用して、アプリをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="34512-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="34512-148">Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="34512-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="34512-149">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="34512-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="34512-150">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="34512-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="34512-151">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="34512-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="34512-152">それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="34512-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="34512-153">完成した `Todo` コンポーネント (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="34512-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="34512-154">コマンド シェルで実行中のアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="34512-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="34512-155">`dotnet run` コマンドを使用して、アプリをリビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="34512-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="34512-156">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="34512-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="34512-157">次の手順</span><span class="sxs-lookup"><span data-stu-id="34512-157">Next steps</span></span>

<span data-ttu-id="34512-158">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="34512-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34512-159">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="34512-159">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="34512-160">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="34512-160">Modify Razor components</span></span>
> * <span data-ttu-id="34512-161">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="34512-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="34512-162">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="34512-162">Use routing in a Blazor app</span></span>

<span data-ttu-id="34512-163">ASP.NET Core Blazor 用のツールについて学習します。</span><span class="sxs-lookup"><span data-stu-id="34512-163">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
