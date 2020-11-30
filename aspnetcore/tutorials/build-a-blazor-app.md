---
title: Blazor Todo リスト アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870439"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="9415a-103">Blazor Todo リスト アプリを構築する</span><span class="sxs-lookup"><span data-stu-id="9415a-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="9415a-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9415a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9415a-105">このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9415a-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="9415a-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9415a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9415a-107">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9415a-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="9415a-108">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="9415a-108">Modify Razor components</span></span>
> * <span data-ttu-id="9415a-109">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="9415a-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="9415a-110">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="9415a-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="9415a-111">このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="9415a-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9415a-112">前提条件</span><span class="sxs-lookup"><span data-stu-id="9415a-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="9415a-113">ToDo リスト Blazor アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="9415a-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="9415a-114">コマンド シェルで `TodoList` という名前の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9415a-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="9415a-115">上記のコマンドでは、アプリを保持するための `TodoList` という名前のフォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9415a-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="9415a-116">`TodoList` フォルダーは、プロジェクトの *ルート フォルダー* です。</span><span class="sxs-lookup"><span data-stu-id="9415a-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="9415a-117">次のコマンドを使用して、ディレクトリを `TodoList` フォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9415a-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="9415a-118">次のコマンドを使用して、新しい `Todo` Razor コンポーネントを、`Pages` フォルダー内のアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="9415a-119">Razor コンポーネント ファイル名の先頭文字は、大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="9415a-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="9415a-120">`Pages` フォルダーを開き、`Todo` コンポーネントのファイル名の先頭が大文字 `T` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9415a-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="9415a-121">ファイル名は `Todo.razor` のはずです。</span><span class="sxs-lookup"><span data-stu-id="9415a-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="9415a-122">`Pages/Todo.razor` で、コンポーネントの最初のマークアップを指定します。</span><span class="sxs-lookup"><span data-stu-id="9415a-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="9415a-123">`Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9415a-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="9415a-124">ナビゲーション バーに `Todo` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="9415a-125">`NavMenu` コンポーネント (`Shared/NavMenu.razor`) はアプリのレイアウトで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9415a-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="9415a-126">レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9415a-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="9415a-127">`Shared/NavMenu.razor` ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加し、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="9415a-128">`Shared/NavMenu.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9415a-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="9415a-129">`TodoList` フォルダーからコマンド シェルで [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) コマンドを実行して、アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9415a-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="9415a-130">`https://localhost:5001/todo` の新しい Todo ページに移動して、サイドバーにある `Todo` コンポーネントへのナビゲーション リンクが機能することを確認します。</span><span class="sxs-lookup"><span data-stu-id="9415a-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="9415a-131">Todo アイテムを表すクラスを保持するために、プロジェクト (`TodoList` フォルダー) のルートに `TodoItem.cs` ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="9415a-132">`TodoItem` クラス用に次の C# コードを使います。</span><span class="sxs-lookup"><span data-stu-id="9415a-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="9415a-133">`Todo` コンポーネント (`Pages/Todo.razor`) に戻ります。</span><span class="sxs-lookup"><span data-stu-id="9415a-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="9415a-134">Todo 項目用のフィールドを `@code` ブロックに追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="9415a-135">`Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="9415a-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="9415a-136">各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="9415a-137">アプリには、リストに Todo 項目を追加するための UI 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="9415a-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="9415a-138">順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9415a-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="9415a-139">`TodoItem.cs` ファイルと更新された `Pages/Todo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9415a-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="9415a-140">コマンド シェルでは、ファイルが保存されるとアプリが自動的に再構築されます。</span><span class="sxs-lookup"><span data-stu-id="9415a-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="9415a-141">ブラウザーからアプリへの接続は一時的に失われ、接続が再確立されるとページが再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9415a-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="9415a-142">**`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーがアタッチされていないためです。</span><span class="sxs-lookup"><span data-stu-id="9415a-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="9415a-143">`Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。</span><span class="sxs-lookup"><span data-stu-id="9415a-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="9415a-144">ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9415a-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="9415a-145">新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。</span><span class="sxs-lookup"><span data-stu-id="9415a-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="9415a-146">指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="9415a-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="9415a-147">`newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。</span><span class="sxs-lookup"><span data-stu-id="9415a-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="9415a-148">`Pages/ToDo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9415a-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="9415a-149">このアプリは、コマンド シェルで自動的にリビルドされます。</span><span class="sxs-lookup"><span data-stu-id="9415a-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="9415a-150">ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9415a-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="9415a-151">各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9415a-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="9415a-152">各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="9415a-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="9415a-153">`@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。</span><span class="sxs-lookup"><span data-stu-id="9415a-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="9415a-154">それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。</span><span class="sxs-lookup"><span data-stu-id="9415a-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="9415a-155">完成した `Todo` コンポーネント (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="9415a-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="9415a-156">`Pages/ToDo.razor` ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="9415a-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="9415a-157">このアプリは、コマンド シェルで自動的にリビルドされます。</span><span class="sxs-lookup"><span data-stu-id="9415a-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="9415a-158">ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9415a-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="9415a-159">Todo アイテムを追加して新しいコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="9415a-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="9415a-160">完了したら、コマンド シェルでアプリをシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="9415a-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="9415a-161">多くのコマンド シェルでは、<kbd>Ctrl</kbd>+<kbd>c</kbd> キーボード コマンドを使用してアプリを停止できます。</span><span class="sxs-lookup"><span data-stu-id="9415a-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9415a-162">次の手順</span><span class="sxs-lookup"><span data-stu-id="9415a-162">Next steps</span></span>

<span data-ttu-id="9415a-163">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="9415a-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9415a-164">Todo リストの Blazor アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9415a-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="9415a-165">Razor コンポーネントを変更する</span><span class="sxs-lookup"><span data-stu-id="9415a-165">Modify Razor components</span></span>
> * <span data-ttu-id="9415a-166">コンポーネントでイベント処理とデータ バインディングを使用する</span><span class="sxs-lookup"><span data-stu-id="9415a-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="9415a-167">Blazor アプリでルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="9415a-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="9415a-168">ASP.NET Core Blazor 用のツールについて学習します。</span><span class="sxs-lookup"><span data-stu-id="9415a-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
