---
title: 'パート 8: 検証を追加する'
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 8。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486123"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="069b7-103">Razor ページのチュートリアル シリーズのパート 8。</span><span class="sxs-lookup"><span data-stu-id="069b7-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="069b7-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="069b7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="069b7-105">ここでは、`Movie` モデルに検証ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="069b7-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="069b7-106">検証規則は、ユーザーがムービーを作成または編集するときに、いつでも適用できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="069b7-107">検証</span><span class="sxs-lookup"><span data-stu-id="069b7-107">Validation</span></span>

<span data-ttu-id="069b7-108">ソフトウェア開発には、[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself" (繰り返しを避けること)) という重要な理念があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="069b7-109">Razor ページでは、機能を一度規定したら、それをアプリ全体に反映する開発を推奨しています。</span><span class="sxs-lookup"><span data-stu-id="069b7-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="069b7-110">DRY は次の場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="069b7-110">DRY can help:</span></span>

* <span data-ttu-id="069b7-111">アプリのコード量を減らす。</span><span class="sxs-lookup"><span data-stu-id="069b7-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="069b7-112">コードのエラーが発生する可能性を低くし、テストと保守を簡単にする。</span><span class="sxs-lookup"><span data-stu-id="069b7-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="069b7-113">Razor Pages と Entity Framework によって提供される検証のサポートは、DRY 原則の好例です。</span><span class="sxs-lookup"><span data-stu-id="069b7-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="069b7-114">検証規則は、1 つの場所 (モデル クラス内) に宣言的に指定されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="069b7-115">規則は、アプリ内のあらゆる場所に適用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="069b7-116">検証規則をムービー モデルに追加する</span><span class="sxs-lookup"><span data-stu-id="069b7-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="069b7-117">`DataAnnotations` 名前空間では以下が提供されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="069b7-118">クラスまたはプロパティに宣言的に適用される一連の組み込みの検証属性。</span><span class="sxs-lookup"><span data-stu-id="069b7-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="069b7-119">書式設定に役立つ、検証を行わない `[DataType]` のような書式設定属性。</span><span class="sxs-lookup"><span data-stu-id="069b7-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="069b7-120">組み込みの `[Required]`、`[StringLength]`、`[RegularExpression]`、および `[Range]` 検証属性を利用するように、`Movie` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="069b7-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="069b7-121">検証属性では、適用対象のモデル プロパティに適用する動作が指定されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="069b7-122">`[Required]` と `[MinimumLength]` の属性は、プロパティに値が必要なことを示します。</span><span class="sxs-lookup"><span data-stu-id="069b7-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="069b7-123">この検証に対応するためにユーザーが空白を入力することを妨げるものは何もありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="069b7-124">`[RegularExpression]` 属性は、入力できる文字を制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="069b7-125">前のコードの `Genre` では:</span><span class="sxs-lookup"><span data-stu-id="069b7-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="069b7-126">文字のみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-126">Must only use letters.</span></span>
  * <span data-ttu-id="069b7-127">最初の文字は大文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="069b7-128">空白、数字、特殊文字は使用できません。</span><span class="sxs-lookup"><span data-stu-id="069b7-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="069b7-129">`RegularExpression` `Rating` では:</span><span class="sxs-lookup"><span data-stu-id="069b7-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="069b7-130">最初の文字が大文字である必要があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="069b7-131">後続のスペースでは、特殊文字と数字が使用できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="069b7-132">"PG-13" は Rating では有効ですが、`Genre` ではエラーになります。</span><span class="sxs-lookup"><span data-stu-id="069b7-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="069b7-133">`[Range]` 属性は、値を指定された範囲内に制限します。</span><span class="sxs-lookup"><span data-stu-id="069b7-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="069b7-134">`[StringLength]` 属性では、文字列プロパティの最大長を設定できます。任意で最小長も設定できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="069b7-135">値の型 (`decimal`、`int`、`float`、`DateTime` など) はもともと必須であり、`[Required]` 属性を必要としません。</span><span class="sxs-lookup"><span data-stu-id="069b7-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="069b7-136">前の検証規則はデモンストレーションのために使用されており、実稼働システムには適していません。</span><span class="sxs-lookup"><span data-stu-id="069b7-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="069b7-137">たとえば、前の例では、2 文字のみのムービーを入力することはできず、`Genre` で特殊文字は許可されません。</span><span class="sxs-lookup"><span data-stu-id="069b7-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="069b7-138">ASP.NET Core によって自動的に適用される検証規則を設定すると、以下のために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="069b7-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="069b7-139">アプリの堅牢性を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="069b7-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="069b7-140">無効なデータがデータベースに保存される可能性を減らします。</span><span class="sxs-lookup"><span data-stu-id="069b7-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="069b7-141">Razor ページの検証エラー UI</span><span class="sxs-lookup"><span data-stu-id="069b7-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="069b7-142">アプリを実行し、[Pages/Movies]/(ページ/ムービー/) に移動します。</span><span class="sxs-lookup"><span data-stu-id="069b7-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="069b7-143">**[Create New]\(新規作成\)** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="069b7-143">Select the **Create New** link.</span></span> <span data-ttu-id="069b7-144">いくつか無効な値を入力してフォームを設定します。</span><span class="sxs-lookup"><span data-stu-id="069b7-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="069b7-145">jQuery クライアント側検証がエラーを検出すると、エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="069b7-147">無効な値を含む各フィールドに、検証エラー メッセージが自動的に表示されることがわかります。</span><span class="sxs-lookup"><span data-stu-id="069b7-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="069b7-148">エラーは、JavaScript と jQuery を使用しているクライアント側で発生し、ユーザーが JavaScript を無効にしている場合はサーバー側でも発生します。</span><span class="sxs-lookup"><span data-stu-id="069b7-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="069b7-149">大きな利点は、[Create]/(作成/) ページまたは [Edit]/(編集/) ページの変更が **必要ない** ことです。</span><span class="sxs-lookup"><span data-stu-id="069b7-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="069b7-150">データ注釈がモデルに適用された後、検証 UI が有効になりました。</span><span class="sxs-lookup"><span data-stu-id="069b7-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="069b7-151">このチュートリアルで作成した Razor Pages では、`Movie` モデル クラスのプロパティの検証属性を使用して、検証規則が自動的に選択されました。</span><span class="sxs-lookup"><span data-stu-id="069b7-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="069b7-152">[Edit]/(編集/) ページを使用して検証をテストすると、同じ検証が適用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="069b7-153">クライアント側の検証エラーがなくなるまで、フォーム データはサーバーにポストされません。</span><span class="sxs-lookup"><span data-stu-id="069b7-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="069b7-154">次のうち 1 つまたは複数の方法で、フォーム データがポストされていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="069b7-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="069b7-155">`OnPostAsync` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="069b7-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="069b7-156">フォームを送信するには、 **[Create]/(作成/)** または **[Save]/(保存/)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="069b7-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="069b7-157">ブレークポイントがヒットすることはありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-157">The break point is never hit.</span></span>
* <span data-ttu-id="069b7-158">[Fiddler ツール](https://www.telerik.com/fiddler)を使用します。</span><span class="sxs-lookup"><span data-stu-id="069b7-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="069b7-159">ブラウザー開発者向けツールを使用して、ネットワーク トラフィックを監視します。</span><span class="sxs-lookup"><span data-stu-id="069b7-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="069b7-160">サーバー側の検証</span><span class="sxs-lookup"><span data-stu-id="069b7-160">Server-side validation</span></span>

<span data-ttu-id="069b7-161">ブラウザーで JavaScript が無効にされている場合、エラーがあるフォームを送信すると、サーバーにポストされます。</span><span class="sxs-lookup"><span data-stu-id="069b7-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="069b7-162">必要に応じて、サーバー側の検証をテストします。</span><span class="sxs-lookup"><span data-stu-id="069b7-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="069b7-163">ブラウザーで JavaScript を無効にします。</span><span class="sxs-lookup"><span data-stu-id="069b7-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="069b7-164">ブラウザーの開発者ツールを使用して JavaScript を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="069b7-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="069b7-165">ブラウザーで JavaScript を無効にすることができない場合は、別のブラウザーを試してください。</span><span class="sxs-lookup"><span data-stu-id="069b7-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="069b7-166">[Create] または [Edit] ページの `OnPostAsync` メソッドにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="069b7-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="069b7-167">無効なデータを含むフォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="069b7-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="069b7-168">モデルの状態が無効であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="069b7-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="069b7-169">または、[サーバーでクライアント側の検証を無効にします](xref:mvc/models/validation#disable-client-side-validation)。</span><span class="sxs-lookup"><span data-stu-id="069b7-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="069b7-170">次のコードは、チュートリアルで前にスキャフォールディング処理した *Create.cshtml* ページの一部を示しています。</span><span class="sxs-lookup"><span data-stu-id="069b7-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="069b7-171">これは、[Create]/(作成/) ページと [Edit]/(編集/) ページで以下を行うために使用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="069b7-172">最初のフォームを表示する。</span><span class="sxs-lookup"><span data-stu-id="069b7-172">Display the initial form.</span></span>
* <span data-ttu-id="069b7-173">エラーが発生した場合にフォームを再表示する。</span><span class="sxs-lookup"><span data-stu-id="069b7-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="069b7-174">[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="069b7-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="069b7-175">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="069b7-176">詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="069b7-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="069b7-177">[Create] ページと [Edit] ページ内に検証規則はありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="069b7-178">検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。</span><span class="sxs-lookup"><span data-stu-id="069b7-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="069b7-179">これらの検証規則は、`Movie` モデルを編集する Razor ページに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="069b7-180">検証ロジックの変更が必要な場合は、モデルでのみ変更します。</span><span class="sxs-lookup"><span data-stu-id="069b7-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="069b7-181">検証は常にアプリケーション全体に適用され、検証ロジックは 1 か所に定義されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="069b7-182">検証が 1 か所であることは、コードが整理された状態を保つことを助け、保守と更新を簡単にします。</span><span class="sxs-lookup"><span data-stu-id="069b7-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="069b7-183">DataType 属性を使用する</span><span class="sxs-lookup"><span data-stu-id="069b7-183">Use DataType Attributes</span></span>

<span data-ttu-id="069b7-184">`Movie` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="069b7-184">Examine the `Movie` class.</span></span> <span data-ttu-id="069b7-185">`System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。</span><span class="sxs-lookup"><span data-stu-id="069b7-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="069b7-186">`[DataType]` 属性は、`ReleaseDate` および `Price` プロパティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="069b7-187">`[DataType]` 属性では、以下が提供されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="069b7-188">ビュー エンジンでデータを書式設定するためのヒント。</span><span class="sxs-lookup"><span data-stu-id="069b7-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="069b7-189">URL の `<a>` や電子メールの `<a href="mailto:EmailAddress.com">` などの属性の指定。</span><span class="sxs-lookup"><span data-stu-id="069b7-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="069b7-190">データの書式を検証するためには、`[RegularExpression]` 属性を使用してください。</span><span class="sxs-lookup"><span data-stu-id="069b7-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="069b7-191">`[DataType]` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="069b7-192">`[DataType]` 属性は、検証属性ではありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="069b7-193">サンプル アプリケーションでは、日付のみが表示され、時刻は表示されません。</span><span class="sxs-lookup"><span data-stu-id="069b7-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="069b7-194">`DataType` 列挙型には、`Date`、`Time`、`PhoneNumber`、`Currency`、`EmailAddress` などの多数のデータ型があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="069b7-195">`[DataType]` 属性は:</span><span class="sxs-lookup"><span data-stu-id="069b7-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="069b7-196">アプリケーションで、型固有の機能を自動的に提供できるようにします。</span><span class="sxs-lookup"><span data-stu-id="069b7-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="069b7-197">たとえば、`DataType.EmailAddress` に対して `mailto:` リンクを作成できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="069b7-198">HTML5 をサポートしているブラウザーに、日付セレクター `DataType.Date` を提供できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="069b7-199">HTML 5 ブラウザーで使用される HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。</span><span class="sxs-lookup"><span data-stu-id="069b7-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="069b7-200">検証が行われることは **ありません**。</span><span class="sxs-lookup"><span data-stu-id="069b7-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="069b7-201">`DataType.Date` は、表示される日付の書式を指定しません。</span><span class="sxs-lookup"><span data-stu-id="069b7-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="069b7-202">既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="069b7-203">`[Column(TypeName = "decimal(18, 2)")]` データ注釈は、Entity Framework Core がデータベースの通貨と `Price` を正しくマッピングできるようにするために必要です。</span><span class="sxs-lookup"><span data-stu-id="069b7-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="069b7-204">詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="069b7-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="069b7-205">`[DisplayFormat]` 属性は、日付の書式を明示的に指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="069b7-206">`ApplyFormatInEditMode` 設定には、編集対象の値を表示するときに適用される書式設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="069b7-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="069b7-207">一部のフィールドでは、この動作は不要です。</span><span class="sxs-lookup"><span data-stu-id="069b7-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="069b7-208">たとえば、通貨値の場合、通常、編集 UI では通貨記号は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="069b7-209">`[DisplayFormat]` 属性を単独で使用できますが、一般的に、`[DataType]` 属性を使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="069b7-210">`[DataType]` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。</span><span class="sxs-lookup"><span data-stu-id="069b7-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="069b7-211">`[DataType]` 属性には、`[DisplayFormat]` では得られない以下のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="069b7-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="069b7-212">ブラウザーで HTML5 機能を有効にすることができます。たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示できます。</span><span class="sxs-lookup"><span data-stu-id="069b7-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="069b7-213">既定では、ブラウザーでは、ロケールに基づく適切な書式を使用してデータがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="069b7-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="069b7-214">`[DataType]` 属性を使用すると、ASP.NET Core フレームワークで、適切なフィールド テンプレートを選択し、データをレンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="069b7-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="069b7-215">`DisplayFormat` を単独で使用する場合、文字列テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="069b7-216">**注:** jQuery の検証は、`[Range]` 属性と `DateTime` では機能しません。</span><span class="sxs-lookup"><span data-stu-id="069b7-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="069b7-217">たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="069b7-218">ベスト プラクティスは、モデル内に日付をハードコーディングしてコンパイルしないようにすることです。したがって、`[Range]` 属性と `DateTime` の使用は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="069b7-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="069b7-219">頻繁に変更される可能性のある日付範囲やその他の値の場合、コード内にそれらを指定するのではなく、[Configuration](xref:fundamentals/configuration/index) を使用します。</span><span class="sxs-lookup"><span data-stu-id="069b7-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="069b7-220">次のコードは、1 行で複数の属性を組み合わせる例です。</span><span class="sxs-lookup"><span data-stu-id="069b7-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="069b7-221">[Razor ページと EF Core の概要](xref:data/ef-rp/intro)に関するページでは、Razor ページでの EF Core 操作についてより詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="069b7-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="069b7-222">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="069b7-222">Apply migrations</span></span>

<span data-ttu-id="069b7-223">クラスに適用された DataAnnotations によって、スキーマが変更されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="069b7-224">たとえば、`Title`フィールドに適用された DataAnnotations は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="069b7-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="069b7-225">文字数を 60 に制限します。</span><span class="sxs-lookup"><span data-stu-id="069b7-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="069b7-226">`null` 値を許可しません。</span><span class="sxs-lookup"><span data-stu-id="069b7-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="069b7-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="069b7-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="069b7-228">`Movie` テーブルには現在、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="069b7-228">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="069b7-229">上記のスキーマ変更では、EF によって例外がスローされることはありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="069b7-230">ただし、スキーマがモデルと一致するように、移行を作成してください。</span><span class="sxs-lookup"><span data-stu-id="069b7-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="069b7-231">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="069b7-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="069b7-232">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="069b7-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="069b7-233">`Update-Database` では、`New_DataAnnotations` クラスの `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="069b7-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="069b7-234">`Up` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="069b7-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="069b7-235">更新された `Movie` テーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="069b7-235">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="069b7-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="069b7-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="069b7-237">SQLite では、移行は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="069b7-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="069b7-238">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="069b7-238">Publish to Azure</span></span>

<span data-ttu-id="069b7-239">Azure へのデプロイの詳細については、「[チュートリアル: SQL Database を使用して Azure に ASP.NET Core アプリを作成する](/azure/app-service/tutorial-dotnetcore-sqldb-app)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="069b7-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="069b7-240">このたびは、この Razor Pages の紹介を最後までお読みいただきありがとうございました。</span><span class="sxs-lookup"><span data-stu-id="069b7-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="069b7-241">このチュートリアルの後は、[Razor Pages と EF Core の概要](xref:data/ef-rp/intro)に関するページにお進みいただくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="069b7-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="069b7-242">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="069b7-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="069b7-243">前へ:新しいフィールドを追加する</span><span class="sxs-lookup"><span data-stu-id="069b7-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
