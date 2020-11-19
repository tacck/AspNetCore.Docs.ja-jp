---
title: 'パート 8: 検証を追加する'
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 8。
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570225"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Razor ページのチュートリアル シリーズのパート 8。

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ここでは、`Movie` モデルに検証ロジックを追加します。 検証規則は、ユーザーがムービーを作成または編集するときに、いつでも適用できます。

## <a name="validation"></a>検証

ソフトウェア開発には、[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself" (繰り返しを避けること)) という重要な理念があります。 Razor ページでは、機能を一度規定したら、それをアプリ全体に反映する開発を推奨しています。 DRY は次の場合に役立ちます。

* アプリのコード量を減らす。
* コードのエラーが発生する可能性を低くし、テストと保守を簡単にする。

Razor Pages と Entity Framework によって提供される検証のサポートは、DRY 原則の好例です。

* 検証規則は、1 つの場所 (モデル クラス内) に宣言的に指定されます。
* 規則は、アプリ内のあらゆる場所に適用されます。

## <a name="add-validation-rules-to-the-movie-model"></a>検証規則をムービー モデルに追加する

`DataAnnotations` 名前空間では以下が提供されます。

* クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性。
* 書式設定に役立つ、検証を行わない `[DataType]` のような書式設定属性。

組み込みの `[Required]`、`[StringLength]`、`[RegularExpression]`、および `[Range]` 検証属性を利用するように、`Movie` クラスを更新します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。

* `[Required]` と `[MinimumLength]` の属性は、プロパティに値が必要なことを示します。 この検証に対応するためにユーザーが空白を入力することを妨げるものは何もありません。
* `[RegularExpression]` 属性は、入力できる文字を制限するために使用されます。 前のコードの `Genre` では:

  * 文字のみを使用する必要があります。
  * 最初の文字は大文字にする必要があります。 空白、数字、特殊文字は使用できません。

* `RegularExpression` `Rating` では:

  * 最初の文字が大文字である必要があります。
  * 後続のスペースでは、特殊文字と数字が使用できます。 "PG-13" は Rating では有効ですが、`Genre` ではエラーになります。

* `[Range]` 属性は、値を指定された範囲内に制限します。
* `[StringLength]` 属性では、文字列プロパティの最大長を設定できます。任意で最小長も設定できます。
* 値の型 (`decimal`、`int`、`float`、`DateTime` など) はもともと必須であり、`[Required]` 属性を必要としません。

前の検証規則はデモンストレーションのために使用されており、実稼働システムには適していません。 たとえば、前の例では、2 文字のみのムービーを入力することはできず、`Genre` で特殊文字は許可されません。

ASP.NET Core によって自動的に適用される検証規則を設定すると、以下のために役立ちます。

* アプリの堅牢性を高めることができます。
* 無効なデータがデータベースに保存される可能性を減らします。

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Razor ページの検証エラー UI

アプリを実行し、[Pages/Movies]/(ページ/ムービー/) に移動します。

**[新規Create]** リンクを選択します。 いくつか無効な値を入力してフォームを設定します。 jQuery クライアント側検証がエラーを検出すると、エラー メッセージが表示されます。

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

無効な値を含む各フィールドに、検証エラー メッセージが自動的に表示されることがわかります。 エラーは、JavaScript と jQuery を使用しているクライアント側で発生し、ユーザーが JavaScript を無効にしている場合はサーバー側でも発生します。

大きな利点は、[Create] または [編集] ページのコードを変更する必要が **なかった** ことです。 データ注釈がモデルに適用された後、検証 UI が有効になりました。 このチュートリアルで作成した Razor Pages では、`Movie` モデル クラスのプロパティの検証属性を使用して、検証規則が自動的に選択されました。 [Edit]/(編集/) ページを使用して検証をテストすると、同じ検証が適用されます。

クライアント側の検証エラーがなくなるまで、フォーム データはサーバーにポストされません。 次のうち 1 つまたは複数の方法で、フォーム データがポストされていないことを確認します。

* `OnPostAsync` メソッドにブレークポイントを設定します。 **[Create]** または **[保存]** を選択してフォームを送信します。 ブレークポイントがヒットすることはありません。
* [Fiddler ツール](https://www.telerik.com/fiddler)を使用します。
* ブラウザー開発者向けツールを使用して、ネットワーク トラフィックを監視します。

### <a name="server-side-validation"></a>サーバー側の検証

ブラウザーで JavaScript が無効にされている場合、エラーがあるフォームを送信すると、サーバーにポストされます。

必要に応じて、サーバー側の検証をテストします。

1. ブラウザーで JavaScript を無効にします。 ブラウザーの開発者ツールを使用して JavaScript を無効にすることができます。 ブラウザーで JavaScript を無効にすることができない場合は、別のブラウザーを試してください。
1. [Create] または [編集] ページの `OnPostAsync` メソッドにブレークポイントを設定します。
1. 無効なデータを含むフォームを送信します。
1. モデルの状態が無効であることを確認します。

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
または、[サーバーでクライアント側の検証を無効にします](xref:mvc/models/validation#disable-client-side-validation)。

次のコードは、チュートリアルで前にスキャフォールディング処理した *Create.cshtml* ページの一部を示しています。 これは、[Create] ページと [編集] ページで、以下を行うために使用されます。

* 最初のフォームを表示する。
* エラーが発生した場合にフォームを再表示する。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。 [検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。 詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。

[Create] ページと [編集] ページに検証規則は含まれません。 検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。 これらの検証規則は、`Movie` モデルを編集する Razor ページに自動的に適用されます。

検証ロジックの変更が必要な場合は、モデルでのみ変更します。 検証は常にアプリケーション全体に適用され、検証ロジックは 1 か所に定義されます。 検証が 1 か所であることは、コードが整理された状態を保つことを助け、保守と更新を簡単にします。

## <a name="use-datatype-attributes"></a>DataType 属性を使用する

`Movie` クラスを調べます。 `System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。 `[DataType]` 属性は、`ReleaseDate` および `Price` プロパティに適用されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`[DataType]` 属性では、以下が提供されます。

* ビュー エンジンでデータを書式設定するためのヒント。
* URL の `<a>` や電子メールの `<a href="mailto:EmailAddress.com">` などの属性の指定。

データの書式を検証するためには、`[RegularExpression]` 属性を使用してください。 `[DataType]` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。 `[DataType]` 属性は、検証属性ではありません。 サンプル アプリケーションでは、日付のみが表示され、時刻は表示されません。

`DataType` 列挙型には、`Date`、`Time`、`PhoneNumber`、`Currency`、`EmailAddress` などの多数のデータ型があります。 

`[DataType]` 属性は:

* アプリケーションで、型固有の機能を自動的に提供できるようにします。 たとえば、`DataType.EmailAddress` に対して `mailto:` リンクを作成できます。
* HTML5 をサポートしているブラウザーに、日付セレクター `DataType.Date` を提供できます。
* HTML 5 ブラウザーで使用される HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。
* 検証が行われることは **ありません**。

`DataType.Date` は、表示される日付の書式を指定しません。 既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。

`[Column(TypeName = "decimal(18, 2)")]` データ注釈は、Entity Framework Core がデータベースの通貨と `Price` を正しくマッピングできるようにするために必要です。 詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。

`[DisplayFormat]` 属性は、日付の書式を明示的に指定するために使用されます。

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 設定には、編集対象の値を表示するときに適用される書式設定を指定します。 一部のフィールドでは、この動作は不要です。 たとえば、通貨値の場合、通常、編集 UI では通貨記号は必要ありません。

`[DisplayFormat]` 属性を単独で使用できますが、一般的に、`[DataType]` 属性を使用することが推奨されます。 `[DataType]` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。 `[DataType]` 属性には、`[DisplayFormat]` では得られない以下のような利点があります。

* ブラウザーで HTML5 機能を有効にすることができます。たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示できます。
* 既定では、ブラウザーでは、ロケールに基づく適切な書式を使用してデータがレンダリングされます。
* `[DataType]` 属性を使用すると、ASP.NET Core フレームワークで、適切なフィールド テンプレートを選択し、データをレンダリングすることができます。 `DisplayFormat` を単独で使用する場合、文字列テンプレートが使用されます。

**注:** jQuery の検証は、`[Range]` 属性と `DateTime` では機能しません。 たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

ベスト プラクティスは、モデル内に日付をハードコーディングしてコンパイルしないようにすることです。したがって、`[Range]` 属性と `DateTime` の使用は推奨されません。 頻繁に変更される可能性のある日付範囲やその他の値の場合、コード内にそれらを指定するのではなく、[Configuration](xref:fundamentals/configuration/index) を使用します。

次のコードは、1 行で複数の属性を組み合わせる例です。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor ページと EF Core の概要](xref:data/ef-rp/intro)に関するページでは、Razor ページでの EF Core 操作についてより詳しく説明されています。

### <a name="apply-migrations"></a>移行を適用する

クラスに適用された DataAnnotations によって、スキーマが変更されます。 たとえば、`Title`フィールドに適用された DataAnnotations は次のようになります。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* 文字数を 60 に制限します。
* `null` 値を許可しません。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie` テーブルには現在、次のスキーマがあります。

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

上記のスキーマ変更では、EF によって例外がスローされることはありません。 ただし、スキーマがモデルと一致するように、移行を作成してください。

**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。
PMC で、次のコマンドを入力します。

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` では、`New_DataAnnotations` クラスの `Up` メソッドが実行されます。 `Up` メソッドを検証します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

更新された `Movie` テーブルには、次のスキーマがあります。

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

SQLite では、移行は必要ありません。

---

### <a name="publish-to-azure"></a>Azure に発行する

Azure へのデプロイの詳細については、「[チュートリアル: SQL Database を使用して Azure に ASP.NET Core アプリを作成する](/azure/app-service/tutorial-dotnetcore-sqldb-app)」をご覧ください。

このたびは、この Razor Pages の紹介を最後までお読みいただきありがとうございました。 このチュートリアルの後は、[Razor Pages と EF Core の概要](xref:data/ef-rp/intro)に関するページにお進みいただくことをお勧めします。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [前へ:新しいフィールドを追加する](xref:tutorials/razor-pages/new-field)
