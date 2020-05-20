---
title: ASP.NET Core の razor 構文リファレンス
author: rick-anderson
description: RazorWeb ページにサーバーベースのコードを埋め込むためのマークアップ構文について説明します。
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424420"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="e114c-103">ASP.NET Core の Razor 構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="e114c-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="e114c-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Taylor Mullen](https://twitter.com/ntaylormullen)、 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="e114c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="e114c-105">Razor は、Web ページにサーバー ベースのコードを埋め込むためのマークアップ構文です。</span><span class="sxs-lookup"><span data-stu-id="e114c-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="e114c-106">Razor 構文は、Razor マークアップ、C#、HTML で構成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="e114c-107">通常、Razor を含むファイルのファイル拡張子は *.cshtml* です。</span><span class="sxs-lookup"><span data-stu-id="e114c-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="e114c-108">Razor は [Razor コンポーネント](xref:blazor/components) ファイル (*.razor*) にもあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="e114c-109">HTML のレンダリング</span><span class="sxs-lookup"><span data-stu-id="e114c-109">Rendering HTML</span></span>

<span data-ttu-id="e114c-110">Razor の既定の言語は HTML です。</span><span class="sxs-lookup"><span data-stu-id="e114c-110">The default Razor language is HTML.</span></span> <span data-ttu-id="e114c-111">Razor マークアップからの HTML のレンダリングは、HTML ファイルからの HTML のレンダリングと同じです。</span><span class="sxs-lookup"><span data-stu-id="e114c-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="e114c-112">*.cshtml* Razor ファイル内の HTML マークアップは、サーバーによって変更されずにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="e114c-113">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="e114c-113">Razor syntax</span></span>

<span data-ttu-id="e114c-114">Razor は C# をサポートし、`@` 記号を使って HTML から C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="e114c-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="e114c-115">Razor は C# の式を評価し、それらを HTML の出力でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="e114c-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="e114c-116">`@` 記号の後に [Razor の予約済みキーワード](#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。</span><span class="sxs-lookup"><span data-stu-id="e114c-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="e114c-117">それ以外の場合は、普通の C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="e114c-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="e114c-118">Razor マークアップで `@` 記号をエスケープするには、`@` 記号をもう 1 つ使います。</span><span class="sxs-lookup"><span data-stu-id="e114c-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="e114c-119">HTML では、コードは 1 つの `@` 記号でレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="e114c-120">メール アドレスを含む HTML の属性とコンテンツは、`@` 記号を遷移文字として扱いません。</span><span class="sxs-lookup"><span data-stu-id="e114c-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="e114c-121">次の例のメール アドレスは、Razor の解析ではそのまま残ります。</span><span class="sxs-lookup"><span data-stu-id="e114c-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="e114c-122">暗黙的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="e114c-122">Implicit Razor expressions</span></span>

<span data-ttu-id="e114c-123">Razor の暗黙的な式は、`@` で始まって C# のコードが続きます。</span><span class="sxs-lookup"><span data-stu-id="e114c-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="e114c-124">C# の `await` キーワードを除き、暗黙的な式にスペースを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="e114c-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="e114c-125">C# のステートメントに明確な終わりがある場合は、スペースを混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="e114c-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="e114c-126">暗黙的な式では、山かっこ (`<>`) の内側の文字は HTML タグとして解釈されるため、C# ジェネリックを含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="e114c-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="e114c-127">次のコードは有効では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="e114c-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="e114c-128">上記のコードでは、次のいずれかのようなコンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="e114c-129">The "int" element wasn't closed.</span><span class="sxs-lookup"><span data-stu-id="e114c-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="e114c-130">All elements must be either self-closing or have a matching end tag. ("int" 要素が閉じられませんでした。すべての要素は、自己終了するか、対応する終了タグが存在する必要があります。)</span><span class="sxs-lookup"><span data-stu-id="e114c-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="e114c-131">メソッド グループ 'GenericMethod' を非デリゲート型 'object' に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="e114c-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="e114c-132">このメソッドを呼び出しますか?</span><span class="sxs-lookup"><span data-stu-id="e114c-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="e114c-133">ジェネリック メソッドの呼び出しは、[明示的な Razor 式](#explicit-razor-expressions)または [Razor コード ブロック](#razor-code-blocks)にラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e114c-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="e114c-134">明示的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="e114c-134">Explicit Razor expressions</span></span>

<span data-ttu-id="e114c-135">明示的な Razor 式は、`@` 記号とバランスの取れたかっこ記号で構成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="e114c-136">1 週間前の時刻を表示するには、次の Razor マークアップを使います。</span><span class="sxs-lookup"><span data-stu-id="e114c-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="e114c-137">`@()` のかっこ内の内容が評価されて、出力にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="e114c-138">前のセクションで説明した暗黙的な式は、一般に、スペースを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="e114c-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="e114c-139">次のコードでは、現在時刻から 1 週間は減算されません。</span><span class="sxs-lookup"><span data-stu-id="e114c-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="e114c-140">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="e114c-141">明示的な式を使うと、テキストと式の結果を連結できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="e114c-142">明示的な式を使わないと、`<p>Age@joe.Age</p>` はメール アドレスとして扱われ、`<p>Age@joe.Age</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="e114c-143">明示的な式として記述すると、`<p>Age33</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="e114c-144">明示的な式を使うと、ジェネリック メソッドから *.cshtml* ファイルに出力できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="e114c-145">次のマークアップは、C# ジェネリックの山かっこによって発生した前述のエラーを修正する方法について示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="e114c-146">コードは明示的な式として書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="e114c-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="e114c-147">式のエンコード</span><span class="sxs-lookup"><span data-stu-id="e114c-147">Expression encoding</span></span>

<span data-ttu-id="e114c-148">文字列として評価される C# の式は、HTML でエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="e114c-149">`IHtmlContent` として評価される C# の式は、`IHtmlContent.WriteTo` によって直接レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="e114c-150">`IHtmlContent` として評価されない C# の式は、`ToString` によって文字列に変換され、レンダリングされる前にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="e114c-151">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="e114c-152">HTML はブラウザーで次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="e114c-153">`HtmlHelper.Raw` の出力はエンコードされませんが、HTML マークアップとしてレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="e114c-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="e114c-154">サニタイズされていないユーザー入力で `HtmlHelper.Raw` を使うと、セキュリティ上のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="e114c-155">ユーザー入力には、悪意のある JavaScript または他の攻撃が含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e114c-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="e114c-156">ユーザー入力をサニタイズすることは困難です。</span><span class="sxs-lookup"><span data-stu-id="e114c-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="e114c-157">ユーザー入力では `HtmlHelper.Raw` を使わないでください。</span><span class="sxs-lookup"><span data-stu-id="e114c-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="e114c-158">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="e114c-159">Razor コード ブロック</span><span class="sxs-lookup"><span data-stu-id="e114c-159">Razor code blocks</span></span>

<span data-ttu-id="e114c-160">Razor コード ブロックは、`@` で始まり、`{}` で囲まれています。</span><span class="sxs-lookup"><span data-stu-id="e114c-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="e114c-161">式とは異なり、コード ブロック内の C# コードはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="e114c-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="e114c-162">ビュー内のコード ブロックと式は同じスコープを共有し、次の順序で定義されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="e114c-163">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e114c-164">コード ブロックで、る[ローカル関数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)をマークアップで宣言し、テンプレート メソッドとしてのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e114c-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="e114c-165">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="e114c-166">暗黙の移行</span><span class="sxs-lookup"><span data-stu-id="e114c-166">Implicit transitions</span></span>

<span data-ttu-id="e114c-167">コード ブロック内の既定の言語は C# ですが、Razor ページは HTML に移行して戻ることがあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="e114c-168">明示的に区切られた遷移</span><span class="sxs-lookup"><span data-stu-id="e114c-168">Explicit delimited transition</span></span>

<span data-ttu-id="e114c-169">HTML をレンダリングする必要のあるコード ブロックのサブセクションを定義するには、レンダリングする文字を Razor の `<text>` タグで囲みます。</span><span class="sxs-lookup"><span data-stu-id="e114c-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="e114c-170">HTML タグによって囲まれていない HTML をレンダリングするには、この方法を使います。</span><span class="sxs-lookup"><span data-stu-id="e114c-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="e114c-171">HTML タグまたは Razor タグがない場合、Razor ラインタイム エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="e114c-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="e114c-172">`<text>` タグは、内容をレンダリングするときに空白文字を制御するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="e114c-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="e114c-173">`<text>` タグの間の内容だけがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="e114c-174">`<text>` タグの前後にある空白文字は HTML の出力には表示されません。</span><span class="sxs-lookup"><span data-stu-id="e114c-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="e114c-175">明示的な行の遷移</span><span class="sxs-lookup"><span data-stu-id="e114c-175">Explicit line transition</span></span>

<span data-ttu-id="e114c-176">残りの行全体をコード ブロック内に HTML としてレンダリングするには、`@:` 構文を使います。</span><span class="sxs-lookup"><span data-stu-id="e114c-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="e114c-177">コードに `@:` がないと、Razor ランタイム エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="e114c-178">Razor ファイルに余分な `@` 文字があると、ブロックの後続のステートメントでコンパイラ エラーが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="e114c-179">これらのコンパイラ エラーは、実際のエラーは報告されたエラーより前で発生しているため、理解するのが難しい場合があります。</span><span class="sxs-lookup"><span data-stu-id="e114c-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="e114c-180">このエラーは、複数の暗黙的/明示的な式を 1 つのコード ブロックに結合した後で発生することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="e114c-181">制御構造</span><span class="sxs-lookup"><span data-stu-id="e114c-181">Control structures</span></span>

<span data-ttu-id="e114c-182">制御構造は、コード ブロックの拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="e114c-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="e114c-183">コード ブロックのすべての側面 (マークアップへの遷移、インライン C# ) が、次の構造にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="e114c-184">条件付き \@if、else if、else、\@switch</span><span class="sxs-lookup"><span data-stu-id="e114c-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="e114c-185">`@if` は、いつコードを実行するかを制御します。</span><span class="sxs-lookup"><span data-stu-id="e114c-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="e114c-186">`else` および `else if` には、`@` 記号は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e114c-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="e114c-187">次のマークアップでは、switch ステートメントの使い方を示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="e114c-188">ループ処理 \@for、\@foreach、\@while、\@do while</span><span class="sxs-lookup"><span data-stu-id="e114c-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="e114c-189">ループ制御ステートメントを使って、テンプレート化された HTML をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="e114c-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="e114c-190">人の一覧をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="e114c-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="e114c-191">以下のループ ステートメントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e114c-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="e114c-192">複合 \@using</span><span class="sxs-lookup"><span data-stu-id="e114c-192">Compound \@using</span></span>

<span data-ttu-id="e114c-193">C# では、オブジェクトを確実に破棄するために `using` オブジェクトが使われています。</span><span class="sxs-lookup"><span data-stu-id="e114c-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="e114c-194">Razor では、同じメカニズムが、追加コンテンツを含む HTML ヘルパーを作成するために使われています。</span><span class="sxs-lookup"><span data-stu-id="e114c-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="e114c-195">次のコードの HTML ヘルパーは、`@using` ステートメントを含む `<form>` タグをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="e114c-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="e114c-196">\@try、catch、finally</span><span class="sxs-lookup"><span data-stu-id="e114c-196">\@try, catch, finally</span></span>

<span data-ttu-id="e114c-197">例外処理は C# に似ています。</span><span class="sxs-lookup"><span data-stu-id="e114c-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="e114c-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="e114c-198">\@lock</span></span>

<span data-ttu-id="e114c-199">Razor には、重要なセクションを lock ステートメントで保護する機能があります。</span><span class="sxs-lookup"><span data-stu-id="e114c-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="e114c-200">コメント</span><span class="sxs-lookup"><span data-stu-id="e114c-200">Comments</span></span>

<span data-ttu-id="e114c-201">Razor は、C# と HTML のコメントをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e114c-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="e114c-202">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="e114c-203">Razor のコメントは、Web ページがレンダリングされる前に、サーバーによって削除されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="e114c-204">Razor では、`@*  *@` を使ってコメントを区切ります。</span><span class="sxs-lookup"><span data-stu-id="e114c-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="e114c-205">次のコードはコメント化されているため、サーバーはどのマークアップもレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="e114c-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="e114c-206">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="e114c-206">Directives</span></span>

<span data-ttu-id="e114c-207">Razor のディレクティブは、`@` 記号の後の予約キーワードによる暗黙的な式で表されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="e114c-208">通常、ディレクティブは、ビューの解析方法を変更したり、異なる機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="e114c-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="e114c-209">Razor がビューのコードを生成する方法を理解すると、ディレクティブの動作を理解しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="e114c-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="e114c-210">上のコードでは、次のようなクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="e114c-211">後の「[ビューに対して生成された Razor C# クラスを調べる](#inspect-the-razor-c-class-generated-for-a-view)」セクションでは、この生成されたクラスを表示する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e114c-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="e114c-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="e114c-212">\@attribute</span></span>

<span data-ttu-id="e114c-213">`@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="e114c-214">次の例では、`[Authorize]` 属性が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="e114c-215">\@code</span><span class="sxs-lookup"><span data-stu-id="e114c-215">\@code</span></span>

<span data-ttu-id="e114c-216">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-217">`@code` ブロックにより、[Razor コンポーネント](xref:blazor/components)では、C# メンバー (フィールド、プロパティ、メソッド) をコンポーネントに追加できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="e114c-218">Razor コンポーネントの場合、 `@code` はのエイリアスであり、よりも優先され [`@functions`](#functions) `@functions` ます。</span><span class="sxs-lookup"><span data-stu-id="e114c-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="e114c-219">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="e114c-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="e114c-220">\@functions</span></span>

<span data-ttu-id="e114c-221">`@functions` ディレクティブでは、生成されたクラスに C# メンバー (フィールド、プロパティ、メソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e114c-222">[Razor コンポーネント](xref:blazor/components)では、`@functions` ではなく `@code` を使用して C# メンバーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e114c-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="e114c-223">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="e114c-224">このコードは、次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="e114c-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="e114c-225">次のコードは、生成された Razor C# クラスです。</span><span class="sxs-lookup"><span data-stu-id="e114c-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e114c-226">`@functions` メソッドは、マークアップが与えられているとき、テンプレート メソッドとしてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e114c-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="e114c-227">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="e114c-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="e114c-228">\@implements</span></span>

<span data-ttu-id="e114c-229">`@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="e114c-230">次の例では、<xref:System.IDisposable.Dispose*> メソッドを呼び出せるように、<xref:System.IDisposable?displayProperty=fullName> が実装されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="e114c-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="e114c-231">\@inherits</span></span>

<span data-ttu-id="e114c-232">`@inherits` ディレクティブは、ビューが継承するクラスの完全な制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="e114c-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="e114c-233">次のコードは、カスタム Razor ページ型です。</span><span class="sxs-lookup"><span data-stu-id="e114c-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="e114c-234">`CustomText` がビューに表示されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="e114c-235">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="e114c-236">`@model` と `@inherits` は同じビューで使うことができます。</span><span class="sxs-lookup"><span data-stu-id="e114c-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="e114c-237">`@inherits` は、ビューがインポートする *_ViewImports.cshtml* ファイルで指定できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="e114c-238">次のコードは、厳密に型指定されたビューの例です。</span><span class="sxs-lookup"><span data-stu-id="e114c-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="e114c-239">モデルに rick@contoso.com が渡された場合、ビューは次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="e114c-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="e114c-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="e114c-240">\@inject</span></span>

<span data-ttu-id="e114c-241">`@inject` ディレクティブを使うと、Razor ページで[サービス コンテナー](xref:fundamentals/dependency-injection)からビューにサービスを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="e114c-242">詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e114c-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="e114c-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="e114c-243">\@layout</span></span>

<span data-ttu-id="e114c-244">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-245">`@layout` ディレクティブにより、Razor コンポーネントのレイアウトが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="e114c-246">レイアウト コンポーネントは、コードの重複や不整合を回避するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="e114c-247">詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="e114c-248">\@model</span><span class="sxs-lookup"><span data-stu-id="e114c-248">\@model</span></span>

<span data-ttu-id="e114c-249">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="e114c-250">`@model` ディレクティブにより、ビューまたはページに渡されるモデルの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="e114c-251">個々のユーザー アカウントで作成された ASP.NET Core MVC または Razor Pages アプリでは、*Views/Account/Login.cshtml* に次のモデル宣言が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e114c-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="e114c-252">生成されるクラスは、`RazorPage<dynamic>` を継承します。</span><span class="sxs-lookup"><span data-stu-id="e114c-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="e114c-253">Razor では、ビューに渡されるモデルにアクセスするための `Model` プロパティが公開されています。</span><span class="sxs-lookup"><span data-stu-id="e114c-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="e114c-254">`@model` ディレクティブにより、`Model` プロパティの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="e114c-255">ディレクティブでは、ビューが派生する生成されたクラスの `T` を `RazorPage<T>` で指定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="e114c-256">`@model` ディレクティブが指定されていない場合、`Model` プロパティは `dynamic` 型になります。</span><span class="sxs-lookup"><span data-stu-id="e114c-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="e114c-257">詳細については、「[厳密に型指定されたモデルと @model キーワード](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="e114c-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="e114c-258">\@namespace</span></span>

<span data-ttu-id="e114c-259">`@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="e114c-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="e114c-260">生成された Razor ページ、MVC ビュー、または Razor コンポーネントのクラスの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="e114c-261">ディレクトリ ツリーで最も近いインポート ファイル (*_ViewImports.cshtml* (ビューまたはページ) または *_Imports.razor* (Razor コンポーネント)) から、ページ、ビュー、またはコンポーネント クラスのルート派生名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="e114c-262">次の表に示す Razor Pages の例の場合:</span><span class="sxs-lookup"><span data-stu-id="e114c-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="e114c-263">各ページで *Pages/_ViewImports.cshtml* がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="e114c-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="e114c-264">*Pages/_ViewImports.cshtml* に `@namespace Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e114c-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="e114c-265">各ページには、その名前空間のルートとして `Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e114c-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="e114c-266">ページ</span><span class="sxs-lookup"><span data-stu-id="e114c-266">Page</span></span>                                        | <span data-ttu-id="e114c-267">名前空間</span><span class="sxs-lookup"><span data-stu-id="e114c-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="e114c-268">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="e114c-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="e114c-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="e114c-271">前のリレーションシップは、MVC ビューと Razor コンポーネントで使用されるインポート ファイルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="e114c-272">複数のインポート ファイルに `@namespace` ディレクティブがあるとき、ディレクトリ ツリーでページ、ビュー、またはコンポーネントに最も近いファイルがルート名前空間の設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="e114c-273">前の例の *EvenMorePages* フォルダーに `@namespace Another.Planet` が含まれるインポート ファイルがある場合 (または、*Pages/MorePages/EvenMorePages/Page.cshtml* ファイルに `@namespace Another.Planet` が含まれる場合)、結果は次の表のようになります。</span><span class="sxs-lookup"><span data-stu-id="e114c-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="e114c-274">ページ</span><span class="sxs-lookup"><span data-stu-id="e114c-274">Page</span></span>                                        | <span data-ttu-id="e114c-275">名前空間</span><span class="sxs-lookup"><span data-stu-id="e114c-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="e114c-276">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="e114c-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="e114c-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="e114c-279">\@page</span><span class="sxs-lookup"><span data-stu-id="e114c-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e114c-280">`@page` ディレクティブには、それが表示されるファイルの型によって、さまざまな効果があります。</span><span class="sxs-lookup"><span data-stu-id="e114c-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="e114c-281">ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="e114c-281">The directive:</span></span>

* <span data-ttu-id="e114c-282">*.cshtml* ファイルでは、ファイルが Razor Page であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="e114c-283">詳細については、「[カスタム ルート](xref:razor-pages/index#custom-routes)」と「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="e114c-284">Razor コンポーネントで要求を直接処理することを指定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="e114c-285">詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e114c-286">*.cshtml* ファイルの最初の行にある `@page` ディレクティブは、ファイルが Razor Page であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="e114c-287">詳細については、「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="e114c-288">\@section</span><span class="sxs-lookup"><span data-stu-id="e114c-288">\@section</span></span>

<span data-ttu-id="e114c-289">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="e114c-290">`@section` ディレクティブを [MVC および Razor Pages レイアウト](xref:mvc/views/layout)と組み合わせて使用すると、HTML ページのさまざまな部分のコンテンツをビューやページでレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="e114c-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="e114c-291">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="e114c-292">\@using</span><span class="sxs-lookup"><span data-stu-id="e114c-292">\@using</span></span>

<span data-ttu-id="e114c-293">`@using` ディレクティブは、生成されるビューに C# の `using` ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="e114c-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e114c-294">[Razor コンポーネント](xref:blazor/components)で `@using` は、は、どのコンポーネントがスコープ内にあるかも制御します。</span><span class="sxs-lookup"><span data-stu-id="e114c-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="e114c-295">ディレクティブ属性</span><span class="sxs-lookup"><span data-stu-id="e114c-295">Directive attributes</span></span>

<span data-ttu-id="e114c-296">Razor ディレクティブの属性は、記号の後に予約済みのキーワードを持つ暗黙的な式で表され `@` ます。</span><span class="sxs-lookup"><span data-stu-id="e114c-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="e114c-297">通常、ディレクティブ属性は、要素の解析方法を変更したり、さまざまな機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="e114c-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="e114c-298">\@attributes</span><span class="sxs-lookup"><span data-stu-id="e114c-298">\@attributes</span></span>

<span data-ttu-id="e114c-299">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-300">`@attributes` では、非宣言属性のレンダリングがコンポーネントに許可されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="e114c-301">詳細については、「<xref:blazor/components#attribute-splatting-and-arbitrary-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="e114c-302">\@bind</span><span class="sxs-lookup"><span data-stu-id="e114c-302">\@bind</span></span>

<span data-ttu-id="e114c-303">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-304">コンポーネントのデータ バインドは、`@bind` 属性によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="e114c-305">詳細については、「<xref:blazor/data-binding>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="e114c-306">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="e114c-306">\@on{EVENT}</span></span>

<span data-ttu-id="e114c-307">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-308">Razor からは、コンポーネントのイベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="e114c-309">詳細については、「<xref:blazor/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="e114c-310">\@on{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="e114c-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="e114c-311">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-312">イベントの既定のアクションを禁止します。</span><span class="sxs-lookup"><span data-stu-id="e114c-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="e114c-313">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="e114c-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="e114c-314">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-315">イベントのイベント伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="e114c-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="e114c-316">\@key</span><span class="sxs-lookup"><span data-stu-id="e114c-316">\@key</span></span>

<span data-ttu-id="e114c-317">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-318">`@key` ディレクティブ属性により、コンポーネントの比較アルゴリズムは、キーの値に基づいて要素またはコンポーネントの保存を保証します。</span><span class="sxs-lookup"><span data-stu-id="e114c-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="e114c-319">詳細については、「<xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="e114c-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="e114c-320">\@ref</span></span>

<span data-ttu-id="e114c-321">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-322">コンポーネント参照 (`@ref`) からは、コンポーネント インスタンスにコマンドを発行できるように、そのインスタンスを参照する方法が与えられます。</span><span class="sxs-lookup"><span data-stu-id="e114c-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="e114c-323">詳細については、「<xref:blazor/components#capture-references-to-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="e114c-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="e114c-324">\@typeparam</span></span>

<span data-ttu-id="e114c-325">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="e114c-326">`@typeparam` ディレクティブによって、生成されるコンポーネント クラスのジェネリック型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="e114c-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="e114c-327">詳細については、「<xref:blazor/templated-components#generic-typed-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e114c-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="e114c-328">テンプレート化された Razor デリゲート</span><span class="sxs-lookup"><span data-stu-id="e114c-328">Templated Razor delegates</span></span>

<span data-ttu-id="e114c-329">Razor テンプレートを使用すると、次の形式で UI スニペットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="e114c-330">次の例では、テンプレート化された Razor デリゲートを <xref:System.Func%602> として指定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="e114c-331">デリゲートによってカプセル化されるメソッドのパラメーターに対しては、[dynamic 型](/dotnet/csharp/programming-guide/types/using-type-dynamic)を指定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="e114c-332">デリゲートの戻り値としては、[object 型](/dotnet/csharp/language-reference/keywords/object)を指定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="e114c-333">テンプレートは、`Name` プロパティを持つ `Pet` の <xref:System.Collections.Generic.List%601> で使用されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="e114c-334">テンプレートは、`foreach` ステートメントによって提供される `pets` で表示されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="e114c-335">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="e114c-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="e114c-336">メソッドへの引数としてインライン Razor テンプレートを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="e114c-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="e114c-337">次の例では、`Repeat` メソッドは Razor テンプレートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="e114c-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="e114c-338">メソッドは、テンプレートを使用して、リストから提供される項目の繰り返しで HTML コンテンツを生成します。</span><span class="sxs-lookup"><span data-stu-id="e114c-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="e114c-339">前の例のペットのリストを使用して、次のように `Repeat` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e114c-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="e114c-340"><xref:System.Collections.Generic.List%601> の `Pet`。</span><span class="sxs-lookup"><span data-stu-id="e114c-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="e114c-341">各ペットを繰り返す回数。</span><span class="sxs-lookup"><span data-stu-id="e114c-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="e114c-342">順不同のリストのリスト項目に対して使用するインライン テンプレート。</span><span class="sxs-lookup"><span data-stu-id="e114c-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="e114c-343">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="e114c-343">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="e114c-344">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="e114c-344">Tag Helpers</span></span>

<span data-ttu-id="e114c-345">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="e114c-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="e114c-346">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)に関する 3 つのディレクティブがあります。</span><span class="sxs-lookup"><span data-stu-id="e114c-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="e114c-347">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="e114c-347">Directive</span></span> | <span data-ttu-id="e114c-348">関数</span><span class="sxs-lookup"><span data-stu-id="e114c-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="e114c-349">ビューでタグ ヘルパーを使えるようにします。</span><span class="sxs-lookup"><span data-stu-id="e114c-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="e114c-350">前に追加したタグ ヘルパーをビューから削除します。</span><span class="sxs-lookup"><span data-stu-id="e114c-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="e114c-351">タグ プレフィックスを指定して、タグ ヘルパーのサポートを有効にしたり、タグ ヘルパーの使用を明示的にしたりします。</span><span class="sxs-lookup"><span data-stu-id="e114c-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="e114c-352">予約済みキーワード</span><span class="sxs-lookup"><span data-stu-id="e114c-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="e114c-353">keywords</span><span class="sxs-lookup"><span data-stu-id="e114c-353"> keywords</span></span>

* <span data-ttu-id="e114c-354">page (ASP.NET Core 2.1 以降を必要とします)</span><span class="sxs-lookup"><span data-stu-id="e114c-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="e114c-355">namespace</span><span class="sxs-lookup"><span data-stu-id="e114c-355">namespace</span></span>
* <span data-ttu-id="e114c-356">functions</span><span class="sxs-lookup"><span data-stu-id="e114c-356">functions</span></span>
* <span data-ttu-id="e114c-357">継承</span><span class="sxs-lookup"><span data-stu-id="e114c-357">inherits</span></span>
* <span data-ttu-id="e114c-358">model</span><span class="sxs-lookup"><span data-stu-id="e114c-358">model</span></span>
* <span data-ttu-id="e114c-359">section</span><span class="sxs-lookup"><span data-stu-id="e114c-359">section</span></span>
* <span data-ttu-id="e114c-360">helper (現在は ASP.NET Core ではサポートされていません)</span><span class="sxs-lookup"><span data-stu-id="e114c-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="e114c-361">キーワードは、でエスケープされ `@(Razor Keyword)` ます (たとえば、 `@(functions)` )。</span><span class="sxs-lookup"><span data-stu-id="e114c-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="e114c-362">C# の Razor キーワード</span><span class="sxs-lookup"><span data-stu-id="e114c-362">C# Razor keywords</span></span>

* <span data-ttu-id="e114c-363">case</span><span class="sxs-lookup"><span data-stu-id="e114c-363">case</span></span>
* <span data-ttu-id="e114c-364">do</span><span class="sxs-lookup"><span data-stu-id="e114c-364">do</span></span>
* <span data-ttu-id="e114c-365">default</span><span class="sxs-lookup"><span data-stu-id="e114c-365">default</span></span>
* <span data-ttu-id="e114c-366">対象</span><span class="sxs-lookup"><span data-stu-id="e114c-366">for</span></span>
* <span data-ttu-id="e114c-367">foreach</span><span class="sxs-lookup"><span data-stu-id="e114c-367">foreach</span></span>
* <span data-ttu-id="e114c-368">if</span><span class="sxs-lookup"><span data-stu-id="e114c-368">if</span></span>
* <span data-ttu-id="e114c-369">else</span><span class="sxs-lookup"><span data-stu-id="e114c-369">else</span></span>
* <span data-ttu-id="e114c-370">lock</span><span class="sxs-lookup"><span data-stu-id="e114c-370">lock</span></span>
* <span data-ttu-id="e114c-371">switch</span><span class="sxs-lookup"><span data-stu-id="e114c-371">switch</span></span>
* <span data-ttu-id="e114c-372">試す</span><span class="sxs-lookup"><span data-stu-id="e114c-372">try</span></span>
* <span data-ttu-id="e114c-373">catch</span><span class="sxs-lookup"><span data-stu-id="e114c-373">catch</span></span>
* <span data-ttu-id="e114c-374">finally</span><span class="sxs-lookup"><span data-stu-id="e114c-374">finally</span></span>
* <span data-ttu-id="e114c-375">using</span><span class="sxs-lookup"><span data-stu-id="e114c-375">using</span></span>
* <span data-ttu-id="e114c-376">while</span><span class="sxs-lookup"><span data-stu-id="e114c-376">while</span></span>

<span data-ttu-id="e114c-377">C# の Razor キーワードは、を使用してダブルエスケープする必要があり `@(@C# Razor Keyword)` ます (例: `@(@case)` )。</span><span class="sxs-lookup"><span data-stu-id="e114c-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="e114c-378">最初のは、 `@` パーサーをエスケープし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="e114c-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="e114c-379">2 番目の `@` は、C# パーサーをエスケープします。</span><span class="sxs-lookup"><span data-stu-id="e114c-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="e114c-380">予約済みキーワードがで使用されていませんRazor</span><span class="sxs-lookup"><span data-stu-id="e114c-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="e114c-381">class</span><span class="sxs-lookup"><span data-stu-id="e114c-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="e114c-382">Razorビューに対して生成された C# クラスを検査する</span><span class="sxs-lookup"><span data-stu-id="e114c-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e114c-383">.NET Core SDK 2.1 以降では、 [ Razor SDK](xref:razor-pages/sdk)によってファイルのコンパイルが処理さ Razor れます。</span><span class="sxs-lookup"><span data-stu-id="e114c-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="e114c-384">プロジェクトをビルドすると、SDK によって、 Razor プロジェクトルートに*obj/<build_configuration> Razor /<target_framework_moniker/* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="e114c-385">ディレクトリ内のディレクトリ構造は、 *Razor* プロジェクトのディレクトリ構造をミラー化します。</span><span class="sxs-lookup"><span data-stu-id="e114c-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="e114c-386">.NET Core 2.1 を対象とする ASP.NET Core 2.1 ページプロジェクトでは、次のディレクトリ構造について考えてみ Razor ます。</span><span class="sxs-lookup"><span data-stu-id="e114c-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="e114c-387">**場所**</span><span class="sxs-lookup"><span data-stu-id="e114c-387">**Areas/**</span></span>
  * <span data-ttu-id="e114c-388">**管理者**</span><span class="sxs-lookup"><span data-stu-id="e114c-388">**Admin/**</span></span>
    * <span data-ttu-id="e114c-389">**トピック**</span><span class="sxs-lookup"><span data-stu-id="e114c-389">**Pages/**</span></span>
      * <span data-ttu-id="e114c-390">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="e114c-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="e114c-392">**トピック**</span><span class="sxs-lookup"><span data-stu-id="e114c-392">**Pages/**</span></span>
  * <span data-ttu-id="e114c-393">**共用**</span><span class="sxs-lookup"><span data-stu-id="e114c-393">**Shared/**</span></span>
    * <span data-ttu-id="e114c-394">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="e114c-395">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="e114c-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="e114c-397">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e114c-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="e114c-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="e114c-399">*Debug* 構成でプロジェクトを作成すると、次の *obj* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="e114c-400">**obj**</span><span class="sxs-lookup"><span data-stu-id="e114c-400">**obj/**</span></span>
  * <span data-ttu-id="e114c-401">**デバック**</span><span class="sxs-lookup"><span data-stu-id="e114c-401">**Debug/**</span></span>
    * <span data-ttu-id="e114c-402">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="e114c-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="e114c-403">**場所**</span><span class="sxs-lookup"><span data-stu-id="e114c-403">**Areas/**</span></span>
          * <span data-ttu-id="e114c-404">**管理者**</span><span class="sxs-lookup"><span data-stu-id="e114c-404">**Admin/**</span></span>
            * <span data-ttu-id="e114c-405">**トピック**</span><span class="sxs-lookup"><span data-stu-id="e114c-405">**Pages/**</span></span>
              * <span data-ttu-id="e114c-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="e114c-407">**トピック**</span><span class="sxs-lookup"><span data-stu-id="e114c-407">**Pages/**</span></span>
          * <span data-ttu-id="e114c-408">**共用**</span><span class="sxs-lookup"><span data-stu-id="e114c-408">**Shared/**</span></span>
            * <span data-ttu-id="e114c-409">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="e114c-410">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="e114c-411">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="e114c-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e114c-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="e114c-413">*Pages/Index. cshtml*の生成されたクラスを表示するには、 *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*を開きます。</span><span class="sxs-lookup"><span data-stu-id="e114c-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="e114c-414">次のクラスを ASP.NET Core MVC プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e114c-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="e114c-415">`Startup.ConfigureServices` で、MVC によって追加された `RazorTemplateEngine` を `CustomTemplateEngine` クラスでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="e114c-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="e114c-416">`CustomTemplateEngine` の `return csharpDocument;` ステートメントにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="e114c-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="e114c-417">プログラムの実行がブレークポイントで停止したら、`generatedCode` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="e114c-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode のテキスト ビジュアライザーの表示](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="e114c-419">ビューの参照と大文字/小文字の区別</span><span class="sxs-lookup"><span data-stu-id="e114c-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="e114c-420">Razorビューエンジンは、ビューに対して大文字と小文字を区別して検索を実行します。</span><span class="sxs-lookup"><span data-stu-id="e114c-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="e114c-421">ただし、実際の参照は、基になるファイル システムによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="e114c-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="e114c-422">ファイル ベースのソース:</span><span class="sxs-lookup"><span data-stu-id="e114c-422">File based source:</span></span>
  * <span data-ttu-id="e114c-423">大文字と小文字が区別されないファイル システムを使っているオペレーティング システム (Windows など) では、物理的なファイル プロバイダーの参照は大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="e114c-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="e114c-424">たとえば、`return View("Test")` は、*/Views/Home/Test.cshtml*、*/Views/home/test.cshtml*、その他のすべての大文字と小文字のバリエーションと一致します。</span><span class="sxs-lookup"><span data-stu-id="e114c-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="e114c-425">大文字と小文字が区別されるファイル システム (たとえば、Linux、OSX、および `EmbeddedFileProvider`) では、参照は大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="e114c-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="e114c-426">たとえば、`return View("Test")` は */Views/Home/Test.cshtml* だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="e114c-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="e114c-427">プリコンパイル済みのビュー: ASP.NET Core 2.0 以降では、プリコンパイル済みのビューの参照は、すべてのオペレーティング システムで大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="e114c-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="e114c-428">動作は、Windows での物理ファイル プロバイダーの動作と同じです。</span><span class="sxs-lookup"><span data-stu-id="e114c-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="e114c-429">2 つのプリコンパイル済みビューの相違点が大文字と小文字の使い分けだけの場合、参照の結果はどちらになるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="e114c-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="e114c-430">開発者には、ファイル名とディレクトリ名の大文字/小文字の使い分けを、次のものと一致させることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e114c-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="e114c-431">領域、コントローラー、アクションの名前。</span><span class="sxs-lookup"><span data-stu-id="e114c-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="e114c-432">トピック.</span><span class="sxs-lookup"><span data-stu-id="e114c-432"> Pages.</span></span>

<span data-ttu-id="e114c-433">大文字と小文字の使い分けを一致させると、展開は基になっているファイル システムに関係なくビューを検索できます。</span><span class="sxs-lookup"><span data-stu-id="e114c-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e114c-434">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e114c-434">Additional resources</span></span>

<span data-ttu-id="e114c-435">を[使用した ASP.NET Web プログラミング Razor の概要構文には、](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)構文を使用したプログラミングの多くのサンプルが用意されて Razor います。</span><span class="sxs-lookup"><span data-stu-id="e114c-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
