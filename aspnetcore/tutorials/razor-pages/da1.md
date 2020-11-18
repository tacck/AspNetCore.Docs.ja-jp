---
title: パート 5、生成されたページの更新
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 5。
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360609"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>パート 5、ASP.NET Core アプリでの生成済みページの更新

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。 **ReleaseDate** は 2 つの単語 (**Release Date**) にする必要があります。

![Chrome で開かれているムービー アプリケーション](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a>生成されたコードの更新

*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

上のコードでは、次のようになります。

* `[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。
* [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) 属性では、フィールドの表示名を指定します。 上のコードでは、"ReleaseDate" ではなく、"Release Date" を指定しています。
* [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。 フィールドに格納されている時刻情報は表示されません。

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。

*Pages/Movies* を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。

![[編集] リンクがマウスでポイントされ、リンク URL として https://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

**[編集]** 、 **[詳細]** 、 **[Delete]** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。

上のコードでは、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)で動的に Razor ページから HTML `href` 属性値 (ルートは相対)、`asp-page`、ルート ID (`asp-route-id`) が生成されます。 詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」をご覧ください。

ブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。 生成された HTML の部分を以下に示します。

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   動的に生成されたリンクでは、クエリ文字列を含むムービー ID が渡されます。 たとえば、`https://localhost:5001/Movies/Details?id=1` の `?id=1` です。

### <a name="add-route-template"></a>ルート テンプレートの追加

`{id:int}` ルート テンプレートを使用するには、[編集]、[詳細]、[Delete] の Razor ページを更新します。 これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。 アプリを実行してから、ソースを表示します。

生成される HTML では、次のように URL のパス部分に ID を追加します。

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

整数を **含まない**、`{id:int}` ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。 たとえば、`https://localhost:5001/Movies/Details` の場合は 404 エラーが返されます。 ID を省略するには、次のように `?` をルート制約に追加します。

```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"` の動作をテストするには:

1. *Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。
1. *Pages/Movies/Details.cshtml.cs* で、`public async Task<IActionResult> OnGetAsync(int? id)` にブレークポイントを設定します。
1. `https://localhost:5001/Movies/Details/` に移動します。

`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。 ルーティング エンジンは、HTTP 404 を返します。 `@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドから `NotFound` (HTTP 404) が返されます。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>コンカレンシーの例外処理の確認

*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。

`catch` ブロックをテストするには、次の操作を行います。

1. `catch (DbUpdateConcurrencyException)` にブレークポイントを設定します。
1. ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。
1. 別のブラウザー ウィンドウで、同じムービーの **[Delete]** リンクを選択してから、ムービーを削除します。
1. 前のブラウザー ウィンドウで、ムービーに変更を投稿します。

実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。 詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。

### <a name="posting-and-binding-review"></a>レビューの投稿とバインディング

*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `https://localhost:5001/Movies/Edit/3`):

* `OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。
* `Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。 *Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用できるようになります。
* [編集] フォームには、ムービーからの値が表示されます。

Movies/Edit ページが投稿された場合:

* ページのフォーム値は `Movie` プロパティにバインドされます。 `[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* モデルの状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、送信された値を含むフォームが再表示されます。
* モデル エラーがない場合、ムービーは保存されます。

[Index]、[Create]、[Delete] Razor ページの HTTP GET メソッドも同様のパターンに従います。 [Create] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。

## <a name="additional-resources"></a>その他のリソース

> [!div class="step-by-step"]
> [前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次へ:検索の追加](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。 **ReleaseDate** は 2 つの単語 (**Release Date**) にする必要があります。

![Chrome で開かれているムービー アプリケーション](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a>生成されたコードの更新

*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。 [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) 属性では、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。 [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性ではデータ型 (`Date`) を指定するため、フィールドに格納される時刻情報は表示されません。

Pages/Movies を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。

![[編集] リンクがマウスでポイントされ、リンク URL として http://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

**[編集]** 、 **[詳細]** 、 **[Delete]** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。 上のコードでは、`AnchorTagHelper` で動的に Razor ページから HTML `href` 属性値 (ルートは相対)、`asp-page`、ルート ID (`asp-route-id`) が生成されます。 詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」を参照してください。

ブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。 生成された HTML の部分を以下に示します。

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

動的に生成されたリンクでは、クエリ文字列を含むムービー ID が渡されます。 たとえば、`https://localhost:5001/Movies/Details?id=1` の `?id=1` です。

"{id:int}" ルート テンプレートを使用するには、[編集]、[詳細]、[Delete] Razor ページを更新します。 これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。 アプリを実行してから、ソースを表示します。 生成される HTML では、次のように URL のパス部分に ID を追加します。

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

整数を **含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。 たとえば、`https://localhost:5001/Movies/Details` の場合は 404 エラーが返されます。 ID を省略するには、次のように `?` をルート制約に追加します。

 ```cshtml
@page "{id:int?}"
```

`@page "{id:int?}"` の動作をテストするには

* *Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。
* *Pages/Movies/Details.cshtml.cs* で、`public async Task<IActionResult> OnGetAsync(int? id)` にブレークポイントを設定します。
* `https://localhost:5001/Movies/Details/` に移動します。

`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。 ルーティング エンジンは、HTTP 404 を返します。 `@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドから `NotFound` (HTTP 404) が返されます。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>コンカレンシーの例外処理の確認

*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。

`catch` ブロックをテストするには、次の操作を行います。

* `catch (DbUpdateConcurrencyException)` へのブレークポイントの設定
* ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。
* 別のブラウザー ウィンドウで、同じムービーの **[Delete]** リンクを選択してから、ムービーを削除します。
* 前のブラウザー ウィンドウで、ムービーに変更を投稿します。

実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。 詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。

### <a name="posting-and-binding-review"></a>レビューの投稿とバインディング

*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `https://localhost:5001/Movies/Edit/3`):

* `OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。 
* `Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。 *Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用できるようになります。
* [編集] フォームには、ムービーからの値が表示されます。

Movies/Edit ページが投稿された場合:

* ページのフォーム値は `Movie` プロパティにバインドされます。 `[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* モデル状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、フォームは送信された値で表示されます。
* モデル エラーがない場合、ムービーは保存されます。

[Index]、[Create]、[Delete] Razor ページの HTTP GET メソッドも同様のパターンに従います。 [Create] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。

次のチュートリアルでは検索を追加します。

## <a name="additional-resources"></a>その他の技術情報

* [このチュートリアルの YouTube バージョン](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次へ:検索の追加](xref:tutorials/razor-pages/search)

::: moniker-end
