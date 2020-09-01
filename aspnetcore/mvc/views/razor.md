---
title: ASP.NET Core の razor 構文リファレンス
author: rick-anderson
description: RazorWeb ページにサーバーベースのコードを埋め込むためのマークアップ構文について説明します。
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 9c2bbd2d463af8a2ea7db716d01bf1436338ea77
ms.sourcegitcommit: cd861463faf44956855e3c4b3669483bbc4a7463
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2020
ms.locfileid: "89101362"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="eb466-103">Razor ASP.NET Core の構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="eb466-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="eb466-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Taylor Mullen](https://twitter.com/ntaylormullen)、 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="eb466-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="eb466-105">Razor は、web ページにサーバーベースのコードを埋め込むためのマークアップ構文です。</span><span class="sxs-lookup"><span data-stu-id="eb466-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="eb466-106">構文は、 Razor Razor マークアップ、C#、および HTML で構成されています。</span><span class="sxs-lookup"><span data-stu-id="eb466-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="eb466-107">に含まれるファイル Razor に *.cshtml*は、通常、拡張子が付いています。</span><span class="sxs-lookup"><span data-stu-id="eb466-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="eb466-108">Razorは、 [ Razor コンポーネント](xref:blazor/components/index)ファイル (*razor*) にもあります。</span><span class="sxs-lookup"><span data-stu-id="eb466-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="eb466-109">HTML のレンダリング</span><span class="sxs-lookup"><span data-stu-id="eb466-109">Rendering HTML</span></span>

<span data-ttu-id="eb466-110">既定の Razor 言語は HTML です。</span><span class="sxs-lookup"><span data-stu-id="eb466-110">The default Razor language is HTML.</span></span> <span data-ttu-id="eb466-111">マークアップから html をレンダリング Razor することは、html ファイルから html をレンダリングする場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="eb466-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="eb466-112">ファイル *内の*HTML マークアップ Razor は、サーバーによって変更されずに表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="eb466-113">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="eb466-113">Razor syntax</span></span>

<span data-ttu-id="eb466-114">Razor C# をサポートし、シンボルを使用して `@` HTML から c# に移行します。</span><span class="sxs-lookup"><span data-stu-id="eb466-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="eb466-115">Razor C# の式を評価し、HTML 出力に表示します。</span><span class="sxs-lookup"><span data-stu-id="eb466-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="eb466-116">記号の `@` 後に[ Razor 予約済みのキーワード](#razor-reserved-keywords)がある場合は、 Razor 固有のマークアップに遷移します。</span><span class="sxs-lookup"><span data-stu-id="eb466-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="eb466-117">それ以外の場合は、普通の C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="eb466-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="eb466-118">マークアップでシンボルをエスケープするには `@` Razor 、2番目のシンボルを使用し `@` ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="eb466-119">HTML では、コードは 1 つの `@` 記号でレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="eb466-120">メール アドレスを含む HTML の属性とコンテンツは、`@` 記号を遷移文字として扱いません。</span><span class="sxs-lookup"><span data-stu-id="eb466-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="eb466-121">次の例の電子メールアドレスは、解析によってそのまま残り Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="eb466-122">暗黙的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="eb466-122">Implicit Razor expressions</span></span>

<span data-ttu-id="eb466-123">暗黙的 Razor な式の先頭に `@` は、次の C# コードが続きます。</span><span class="sxs-lookup"><span data-stu-id="eb466-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="eb466-124">C# の `await` キーワードを除き、暗黙的な式にスペースを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="eb466-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="eb466-125">C# のステートメントに明確な終わりがある場合は、スペースを混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="eb466-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="eb466-126">暗黙的な式では、山かっこ (`<>`) の内側の文字は HTML タグとして解釈されるため、C# ジェネリックを含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="eb466-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="eb466-127">次のコードは有効では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="eb466-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="eb466-128">上記のコードでは、次のいずれかのようなコンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="eb466-129">The "int" element wasn't closed.</span><span class="sxs-lookup"><span data-stu-id="eb466-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="eb466-130">All elements must be either self-closing or have a matching end tag. ("int" 要素が閉じられませんでした。すべての要素は、自己終了するか、対応する終了タグが存在する必要があります。)</span><span class="sxs-lookup"><span data-stu-id="eb466-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="eb466-131">メソッド グループ 'GenericMethod' を非デリゲート型 'object' に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="eb466-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="eb466-132">このメソッドを呼び出しますか?</span><span class="sxs-lookup"><span data-stu-id="eb466-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="eb466-133">ジェネリックメソッドの呼び出しは、[明示的な Razor 式](#explicit-razor-expressions)または[ Razor コードブロック](#razor-code-blocks)でラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="eb466-134">明示的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="eb466-134">Explicit Razor expressions</span></span>

<span data-ttu-id="eb466-135">明示的な Razor 式は、 `@` 均衡かっこ付きの記号で構成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="eb466-136">先週の時間を表示するには、次の Razor マークアップを使用します。</span><span class="sxs-lookup"><span data-stu-id="eb466-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="eb466-137">`@()` のかっこ内の内容が評価されて、出力にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="eb466-138">前のセクションで説明した暗黙的な式は、一般に、スペースを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="eb466-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="eb466-139">次のコードでは、現在時刻から 1 週間は減算されません。</span><span class="sxs-lookup"><span data-stu-id="eb466-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="eb466-140">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="eb466-141">明示的な式を使うと、テキストと式の結果を連結できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="eb466-142">明示的な式を使わないと、`<p>Age@joe.Age</p>` はメール アドレスとして扱われ、`<p>Age@joe.Age</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="eb466-143">明示的な式として記述すると、`<p>Age33</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="eb466-144">明示的な式を使うと、ジェネリック メソッドから *.cshtml* ファイルに出力できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="eb466-145">次のマークアップは、C# ジェネリックの山かっこによって発生した前述のエラーを修正する方法について示します。</span><span class="sxs-lookup"><span data-stu-id="eb466-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="eb466-146">コードは明示的な式として書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eb466-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="eb466-147">式のエンコード</span><span class="sxs-lookup"><span data-stu-id="eb466-147">Expression encoding</span></span>

<span data-ttu-id="eb466-148">文字列として評価される C# の式は、HTML でエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="eb466-149">`IHtmlContent` として評価される C# の式は、`IHtmlContent.WriteTo` によって直接レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="eb466-150">`IHtmlContent` として評価されない C# の式は、`ToString` によって文字列に変換され、レンダリングされる前にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="eb466-151">上記のコードは、次の HTML をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="eb466-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="eb466-152">HTML は、プレーンテキストとしてブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="eb466-153">&lt;スパン &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="eb466-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="eb466-154">`HtmlHelper.Raw` の出力はエンコードされませんが、HTML マークアップとしてレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="eb466-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="eb466-155">サニタイズされていないユーザー入力で `HtmlHelper.Raw` を使うと、セキュリティ上のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="eb466-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="eb466-156">ユーザー入力には、悪意のある JavaScript または他の攻撃が含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="eb466-157">ユーザー入力をサニタイズすることは困難です。</span><span class="sxs-lookup"><span data-stu-id="eb466-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="eb466-158">ユーザー入力では `HtmlHelper.Raw` を使わないでください。</span><span class="sxs-lookup"><span data-stu-id="eb466-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="eb466-159">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="eb466-160">Razor コードブロック</span><span class="sxs-lookup"><span data-stu-id="eb466-160">Razor code blocks</span></span>

<span data-ttu-id="eb466-161">Razor コードブロックはで始まり `@` 、で囲まれて `{}` います。</span><span class="sxs-lookup"><span data-stu-id="eb466-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="eb466-162">式とは異なり、コード ブロック内の C# コードはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="eb466-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="eb466-163">ビュー内のコード ブロックと式は同じスコープを共有し、次の順序で定義されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="eb466-164">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb466-165">コード ブロックで、る[ローカル関数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)をマークアップで宣言し、テンプレート メソッドとしてのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="eb466-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="eb466-166">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="eb466-167">暗黙の移行</span><span class="sxs-lookup"><span data-stu-id="eb466-167">Implicit transitions</span></span>

<span data-ttu-id="eb466-168">コードブロックの既定の言語は C# ですが、 Razor ページは HTML に戻ることができます。</span><span class="sxs-lookup"><span data-stu-id="eb466-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="eb466-169">明示的に区切られた遷移</span><span class="sxs-lookup"><span data-stu-id="eb466-169">Explicit delimited transition</span></span>

<span data-ttu-id="eb466-170">HTML を表示する必要があるコードブロックのサブセクションを定義するには、タグを使用して、表示する文字を囲み Razor `<text>` ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="eb466-171">HTML タグによって囲まれていない HTML をレンダリングするには、この方法を使います。</span><span class="sxs-lookup"><span data-stu-id="eb466-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="eb466-172">HTML またはタグがないと Razor 、 Razor ランタイムエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="eb466-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="eb466-173">`<text>` タグは、内容をレンダリングするときに空白文字を制御するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="eb466-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="eb466-174">`<text>` タグの間の内容だけがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="eb466-175">`<text>` タグの前後にある空白文字は HTML の出力には表示されません。</span><span class="sxs-lookup"><span data-stu-id="eb466-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="eb466-176">明示的な行の遷移</span><span class="sxs-lookup"><span data-stu-id="eb466-176">Explicit line transition</span></span>

<span data-ttu-id="eb466-177">残りの行全体をコード ブロック内に HTML としてレンダリングするには、`@:` 構文を使います。</span><span class="sxs-lookup"><span data-stu-id="eb466-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="eb466-178">コードにを指定しないと `@:` 、 Razor ランタイムエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="eb466-179">`@`ファイルに余分な文字が含まれ Razor ていると、ステートメントの後のブロックで、コンパイラエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="eb466-180">これらのコンパイラ エラーは、実際のエラーは報告されたエラーより前で発生しているため、理解するのが難しい場合があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="eb466-181">このエラーは、複数の暗黙的/明示的な式を 1 つのコード ブロックに結合した後で発生することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="eb466-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="eb466-182">制御構造</span><span class="sxs-lookup"><span data-stu-id="eb466-182">Control structures</span></span>

<span data-ttu-id="eb466-183">制御構造は、コード ブロックの拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="eb466-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="eb466-184">コード ブロックのすべての側面 (マークアップへの遷移、インライン C# ) が、次の構造にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="eb466-185">条件 `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="eb466-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="eb466-186">`@if` は、いつコードを実行するかを制御します。</span><span class="sxs-lookup"><span data-stu-id="eb466-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="eb466-187">`else` および `else if` には、`@` 記号は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="eb466-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="eb466-188">次のマークアップでは、switch ステートメントの使い方を示します。</span><span class="sxs-lookup"><span data-stu-id="eb466-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="eb466-189">サウンド `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="eb466-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="eb466-190">ループ制御ステートメントを使って、テンプレート化された HTML をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="eb466-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="eb466-191">人の一覧をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="eb466-191">To render a list of people:</span></span>

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

<span data-ttu-id="eb466-192">以下のループ ステートメントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="eb466-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="eb466-193">複合 `@using`</span><span class="sxs-lookup"><span data-stu-id="eb466-193">Compound `@using`</span></span>

<span data-ttu-id="eb466-194">C# では、オブジェクトを確実に破棄するために `using` オブジェクトが使われています。</span><span class="sxs-lookup"><span data-stu-id="eb466-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="eb466-195">で Razor は、同じメカニズムを使用して、追加のコンテンツを含む HTML ヘルパーを作成します。</span><span class="sxs-lookup"><span data-stu-id="eb466-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="eb466-196">次のコードの HTML ヘルパーは、`@using` ステートメントを含む `<form>` タグをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="eb466-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="eb466-197">例外処理は C# に似ています。</span><span class="sxs-lookup"><span data-stu-id="eb466-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="eb466-198">Razor には、次のように、重要なセクションを lock ステートメントで保護する機能があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="eb466-199">コメント</span><span class="sxs-lookup"><span data-stu-id="eb466-199">Comments</span></span>

<span data-ttu-id="eb466-200">Razor C# および HTML コメントをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eb466-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="eb466-201">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="eb466-202">Razor コメントは、web ページが表示される前にサーバーによって削除されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="eb466-203">Razor は `@*  *@` 、を使用してコメントを区切ります。</span><span class="sxs-lookup"><span data-stu-id="eb466-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="eb466-204">次のコードはコメント化されているため、サーバーはどのマークアップもレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="eb466-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="eb466-205">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="eb466-205">Directives</span></span>

<span data-ttu-id="eb466-206">Razor ディレクティブは、記号の後に予約済みのキーワードを持つ暗黙的な式で表され `@` ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="eb466-207">通常、ディレクティブは、ビューの解析方法を変更したり、異なる機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="eb466-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="eb466-208">が Razor ビューのコードを生成する方法を理解すると、ディレクティブのしくみを理解しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="eb466-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="eb466-209">上のコードでは、次のようなクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="eb466-210">この記事の後半で、「 [ Razor ビューに対して生成された C# クラスを調べる](#inspect-the-razor-c-class-generated-for-a-view) 」セクションでは、この生成されたクラスを表示する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="eb466-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="eb466-211">`@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="eb466-212">次の例では、`[Authorize]` 属性が追加されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="eb466-213">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-214">この `@code` ブロックにより、 [ Razor コンポーネント](xref:blazor/components/index)は、コンポーネントに C# のメンバー (フィールド、プロパティ、およびメソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="eb466-215">コンポーネントの場合 Razor 、 `@code` はのエイリアスであり、よりも優先され [`@functions`](#functions) `@functions` ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="eb466-216">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="eb466-217">`@functions` ディレクティブでは、生成されたクラスに C# メンバー (フィールド、プロパティ、メソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb466-218">[ [ Razor コンポーネント](xref:blazor/components/index)] で、を使用して `@code` `@functions` C# メンバーを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb466-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="eb466-219">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="eb466-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="eb466-220">このコードは、次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="eb466-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="eb466-221">次のコードは、生成された Razor C# クラスです。</span><span class="sxs-lookup"><span data-stu-id="eb466-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb466-222">`@functions` メソッドは、マークアップが与えられているとき、テンプレート メソッドとしてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="eb466-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="eb466-223">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="eb466-224">`@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="eb466-225">次の例では、<xref:System.IDisposable.Dispose*> メソッドを呼び出せるように、<xref:System.IDisposable?displayProperty=fullName> が実装されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### `@inherits`

<span data-ttu-id="eb466-226">`@inherits` ディレクティブは、ビューが継承するクラスの完全な制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="eb466-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="eb466-227">次のコードは、カスタム Razor ページの種類です。</span><span class="sxs-lookup"><span data-stu-id="eb466-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="eb466-228">`CustomText` がビューに表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="eb466-229">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="eb466-230">`@model` と `@inherits` は同じビューで使うことができます。</span><span class="sxs-lookup"><span data-stu-id="eb466-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="eb466-231">`@inherits` は、ビューがインポートする *_ViewImports.cshtml* ファイルで指定できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="eb466-232">次のコードは、厳密に型指定されたビューの例です。</span><span class="sxs-lookup"><span data-stu-id="eb466-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="eb466-233">モデルに rick@contoso.com が渡された場合、ビューは次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="eb466-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="eb466-234">ディレクティブを使用すると、サービス `@inject` Razor [コンテナー](xref:fundamentals/dependency-injection) からビューにサービスを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="eb466-235">詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="eb466-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="eb466-236">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-237">ディレクティブは、 `@layout` コンポーネントのレイアウトを指定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-237">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="eb466-238">レイアウト コンポーネントは、コードの重複や不整合を回避するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="eb466-239">詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="eb466-240">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="eb466-241">`@model` ディレクティブにより、ビューまたはページに渡されるモデルの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="eb466-242">Razor個々のユーザーアカウントで作成された ASP.NET CORE MVC またはページアプリでは、 *Views/Account/Login. cshtml*には次のモデル宣言が含まれています。</span><span class="sxs-lookup"><span data-stu-id="eb466-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="eb466-243">生成されるクラスは、`RazorPage<dynamic>` を継承します。</span><span class="sxs-lookup"><span data-stu-id="eb466-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="eb466-244">Razor`Model`ビューに渡されるモデルにアクセスするためのプロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="eb466-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="eb466-245">`@model` ディレクティブにより、`Model` プロパティの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="eb466-246">ディレクティブでは、ビューが派生する生成されたクラスの `T` を `RazorPage<T>` で指定します。</span><span class="sxs-lookup"><span data-stu-id="eb466-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="eb466-247">`@model` ディレクティブが指定されていない場合、`Model` プロパティは `dynamic` 型になります。</span><span class="sxs-lookup"><span data-stu-id="eb466-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="eb466-248">詳細については、「 [厳密に型指定されたモデルと @model キーワード](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="eb466-249">`@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="eb466-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="eb466-250">生成された Razor ページ、MVC ビュー、またはコンポーネントのクラスの名前空間を設定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="eb466-251">ページ、ビュー、またはコンポーネントクラスのルート派生名前空間を、ディレクトリツリー内の最も近いインポートファイル、 *_ViewImports* (ビューまたはページ)、または *_Imports razor* ( Razor コンポーネント) に設定します。</span><span class="sxs-lookup"><span data-stu-id="eb466-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="eb466-252">次の表に示すページの例については、「」を参照して Razor ください。</span><span class="sxs-lookup"><span data-stu-id="eb466-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="eb466-253">各ページで *Pages/_ViewImports.cshtml* がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="eb466-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="eb466-254">*Pages/_ViewImports.cshtml* に `@namespace Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="eb466-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="eb466-255">各ページには、その名前空間のルートとして `Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="eb466-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="eb466-256">Page</span><span class="sxs-lookup"><span data-stu-id="eb466-256">Page</span></span>                                        | <span data-ttu-id="eb466-257">名前空間</span><span class="sxs-lookup"><span data-stu-id="eb466-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="eb466-258">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="eb466-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="eb466-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="eb466-261">前のリレーションシップは、MVC ビューおよびコンポーネントで使用されるファイルのインポートに適用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="eb466-262">複数のインポート ファイルに `@namespace` ディレクティブがあるとき、ディレクトリ ツリーでページ、ビュー、またはコンポーネントに最も近いファイルがルート名前空間の設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="eb466-263">前の例の *EvenMorePages* フォルダーに `@namespace Another.Planet` が含まれるインポート ファイルがある場合 (または、*Pages/MorePages/EvenMorePages/Page.cshtml* ファイルに `@namespace Another.Planet` が含まれる場合)、結果は次の表のようになります。</span><span class="sxs-lookup"><span data-stu-id="eb466-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="eb466-264">Page</span><span class="sxs-lookup"><span data-stu-id="eb466-264">Page</span></span>                                        | <span data-ttu-id="eb466-265">名前空間</span><span class="sxs-lookup"><span data-stu-id="eb466-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="eb466-266">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="eb466-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="eb466-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb466-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb466-269">`@page` ディレクティブには、それが表示されるファイルの型によって、さまざまな効果があります。</span><span class="sxs-lookup"><span data-stu-id="eb466-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="eb466-270">ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="eb466-270">The directive:</span></span>

* <span data-ttu-id="eb466-271">は、ファイルがページであることを示し*ます。* Razor</span><span class="sxs-lookup"><span data-stu-id="eb466-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="eb466-272">詳細については、「[カスタム ルート](xref:razor-pages/index#custom-routes)」と「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="eb466-273">コンポーネントが要求を直接処理する必要があることを指定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="eb466-274">詳細については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb466-275">`@page`ファイルの先頭行にあるディレクティブは、ファイルがページであることを示し*ます。* Razor</span><span class="sxs-lookup"><span data-stu-id="eb466-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="eb466-276">詳細については、「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="eb466-277">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-277">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="eb466-278">ディレクティブは、 `@section` ビューまたはページが HTML ページのさまざまな部分でコンテンツを表示できるようにするために、 [MVC および Razor ページレイアウト](xref:mvc/views/layout) と組み合わせて使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-278">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="eb466-279">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-279">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="eb466-280">`@using` ディレクティブは、生成されるビューに C# の `using` ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb466-280">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb466-281">[ Razor コンポーネント](xref:blazor/components/index)で `@using` は、スコープ内のコンポーネントも制御します。</span><span class="sxs-lookup"><span data-stu-id="eb466-281">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="eb466-282">ディレクティブ属性</span><span class="sxs-lookup"><span data-stu-id="eb466-282">Directive attributes</span></span>

<span data-ttu-id="eb466-283">Razor ディレクティブ属性は、記号の後に予約済みのキーワードを使用した暗黙的な式によって表され `@` ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-283">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="eb466-284">通常、ディレクティブ属性は、要素の解析方法を変更したり、さまざまな機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="eb466-284">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="eb466-285">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-285">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-286">`@attributes` では、非宣言属性のレンダリングがコンポーネントに許可されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-286">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="eb466-287">詳細については、「<xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-287">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="eb466-288">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-288">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-289">コンポーネントのデータ バインドは、`@bind` 属性によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-289">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="eb466-290">詳細については、「<xref:blazor/components/data-binding>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-290">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="eb466-291">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-292">Razor コンポーネントのイベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="eb466-292">Razor provides event handling features for components.</span></span> <span data-ttu-id="eb466-293">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-293">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="eb466-294">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-295">イベントの既定のアクションを禁止します。</span><span class="sxs-lookup"><span data-stu-id="eb466-295">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="eb466-296">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-296">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-297">イベントのイベント伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="eb466-297">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="eb466-298">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-298">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-299">`@key` ディレクティブ属性により、コンポーネントの比較アルゴリズムは、キーの値に基づいて要素またはコンポーネントの保存を保証します。</span><span class="sxs-lookup"><span data-stu-id="eb466-299">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="eb466-300">詳細については、「<xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-300">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="eb466-301">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-302">コンポーネント参照 (`@ref`) からは、コンポーネント インスタンスにコマンドを発行できるように、そのインスタンスを参照する方法が与えられます。</span><span class="sxs-lookup"><span data-stu-id="eb466-302">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="eb466-303">詳細については、「<xref:blazor/components/index#capture-references-to-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-303">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="eb466-304">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="eb466-305">`@typeparam` ディレクティブによって、生成されるコンポーネント クラスのジェネリック型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="eb466-305">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="eb466-306">詳細については、「<xref:blazor/components/templated-components#generic-typed-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb466-306">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="eb466-307">テンプレート化 Razor デリゲート</span><span class="sxs-lookup"><span data-stu-id="eb466-307">Templated Razor delegates</span></span>

<span data-ttu-id="eb466-308">Razor テンプレートを使用すると、次の形式で UI スニペットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-308">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="eb466-309">次の例は、テンプレート化されたデリゲートをとして指定する方法を示してい Razor <xref:System.Func%602> ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-309">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="eb466-310">デリゲートによってカプセル化されるメソッドのパラメーターに対しては、[dynamic 型](/dotnet/csharp/programming-guide/types/using-type-dynamic)を指定します。</span><span class="sxs-lookup"><span data-stu-id="eb466-310">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="eb466-311">デリゲートの戻り値としては、[object 型](/dotnet/csharp/language-reference/keywords/object)を指定します。</span><span class="sxs-lookup"><span data-stu-id="eb466-311">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="eb466-312">テンプレートは、`Name` プロパティを持つ `Pet` の <xref:System.Collections.Generic.List%601> で使用されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-312">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="eb466-313">テンプレートは、`foreach` ステートメントによって提供される `pets` で表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-313">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="eb466-314">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="eb466-314">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="eb466-315">Razorメソッドの引数としてインラインテンプレートを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="eb466-315">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="eb466-316">次の例では、 `Repeat` メソッドがテンプレートを受け取り Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-316">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="eb466-317">メソッドは、テンプレートを使用して、リストから提供される項目の繰り返しで HTML コンテンツを生成します。</span><span class="sxs-lookup"><span data-stu-id="eb466-317">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="eb466-318">前の例のペットのリストを使用して、次のように `Repeat` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb466-318">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="eb466-319"><xref:System.Collections.Generic.List%601> の `Pet`。</span><span class="sxs-lookup"><span data-stu-id="eb466-319"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="eb466-320">各ペットを繰り返す回数。</span><span class="sxs-lookup"><span data-stu-id="eb466-320">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="eb466-321">順不同のリストのリスト項目に対して使用するインライン テンプレート。</span><span class="sxs-lookup"><span data-stu-id="eb466-321">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="eb466-322">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="eb466-322">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="eb466-323">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="eb466-323">Tag Helpers</span></span>

<span data-ttu-id="eb466-324">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="eb466-324">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="eb466-325">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)に関する 3 つのディレクティブがあります。</span><span class="sxs-lookup"><span data-stu-id="eb466-325">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="eb466-326">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="eb466-326">Directive</span></span> | <span data-ttu-id="eb466-327">関数</span><span class="sxs-lookup"><span data-stu-id="eb466-327">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="eb466-328">ビューでタグ ヘルパーを使えるようにします。</span><span class="sxs-lookup"><span data-stu-id="eb466-328">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="eb466-329">前に追加したタグ ヘルパーをビューから削除します。</span><span class="sxs-lookup"><span data-stu-id="eb466-329">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="eb466-330">タグ プレフィックスを指定して、タグ ヘルパーのサポートを有効にしたり、タグ ヘルパーの使用を明示的にしたりします。</span><span class="sxs-lookup"><span data-stu-id="eb466-330">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="eb466-331">Razor 予約済みキーワード</span><span class="sxs-lookup"><span data-stu-id="eb466-331">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="eb466-332">Razor keywords</span><span class="sxs-lookup"><span data-stu-id="eb466-332">Razor keywords</span></span>

* <span data-ttu-id="eb466-333">`page` (ASP.NET Core 2.1 以降が必要)</span><span class="sxs-lookup"><span data-stu-id="eb466-333">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="eb466-334">`helper` (現在 ASP.NET Core ではサポートされていません)</span><span class="sxs-lookup"><span data-stu-id="eb466-334">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="eb466-335">Razor キーワードは、でエスケープされ `@(Razor Keyword)` ます (たとえば、 `@(functions)` )。</span><span class="sxs-lookup"><span data-stu-id="eb466-335">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="eb466-336">C# の Razor キーワード</span><span class="sxs-lookup"><span data-stu-id="eb466-336">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="eb466-337">C# の Razor キーワードは、を使用してダブルエスケープする必要があり `@(@C# Razor Keyword)` ます (例: `@(@case)` )。</span><span class="sxs-lookup"><span data-stu-id="eb466-337">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="eb466-338">最初のは、 `@` パーサーをエスケープし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-338">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="eb466-339">2 番目の `@` は、C# パーサーをエスケープします。</span><span class="sxs-lookup"><span data-stu-id="eb466-339">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="eb466-340">予約済みキーワードがで使用されていません Razor</span><span class="sxs-lookup"><span data-stu-id="eb466-340">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="eb466-341">Razorビューに対して生成された C# クラスを検査する</span><span class="sxs-lookup"><span data-stu-id="eb466-341">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb466-342">.NET Core SDK 2.1 以降では、 [ Razor SDK](xref:razor-pages/sdk)によってファイルのコンパイルが処理さ Razor れます。</span><span class="sxs-lookup"><span data-stu-id="eb466-342">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="eb466-343">プロジェクトをビルドすると、SDK によって、 Razor プロジェクトルートに*obj/<build_configuration> Razor /<target_framework_moniker/* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-343">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="eb466-344">ディレクトリ内のディレクトリ構造は、 *Razor* プロジェクトのディレクトリ構造をミラー化します。</span><span class="sxs-lookup"><span data-stu-id="eb466-344">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="eb466-345">.NET Core 2.1 を対象とする ASP.NET Core 2.1 ページプロジェクトでは、次のディレクトリ構造について考えてみ Razor ます。</span><span class="sxs-lookup"><span data-stu-id="eb466-345">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="eb466-346">*Debug* 構成でプロジェクトを作成すると、次の *obj* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-346">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="eb466-347">*Pages/Index. cshtml*の生成されたクラスを表示するには、 *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*を開きます。</span><span class="sxs-lookup"><span data-stu-id="eb466-347">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="eb466-348">次のクラスを ASP.NET Core MVC プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="eb466-348">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="eb466-349">`Startup.ConfigureServices` で、MVC によって追加された `RazorTemplateEngine` を `CustomTemplateEngine` クラスでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="eb466-349">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="eb466-350">`CustomTemplateEngine` の `return csharpDocument;` ステートメントにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="eb466-350">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="eb466-351">プログラムの実行がブレークポイントで停止したら、`generatedCode` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="eb466-351">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode のテキスト ビジュアライザーの表示](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="eb466-353">ビューの参照と大文字/小文字の区別</span><span class="sxs-lookup"><span data-stu-id="eb466-353">View lookups and case sensitivity</span></span>

<span data-ttu-id="eb466-354">Razorビューエンジンは、ビューに対して大文字と小文字を区別して検索を実行します。</span><span class="sxs-lookup"><span data-stu-id="eb466-354">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="eb466-355">ただし、実際の参照は、基になるファイル システムによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="eb466-355">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="eb466-356">ファイル ベースのソース:</span><span class="sxs-lookup"><span data-stu-id="eb466-356">File based source:</span></span>
  * <span data-ttu-id="eb466-357">大文字と小文字が区別されないファイル システムを使っているオペレーティング システム (Windows など) では、物理的なファイル プロバイダーの参照は大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="eb466-357">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="eb466-358">たとえば、`return View("Test")` は、*/Views/Home/Test.cshtml*、*/Views/home/test.cshtml*、その他のすべての大文字と小文字のバリエーションと一致します。</span><span class="sxs-lookup"><span data-stu-id="eb466-358">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="eb466-359">大文字と小文字が区別されるファイル システム (たとえば、Linux、OSX、および `EmbeddedFileProvider`) では、参照は大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="eb466-359">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="eb466-360">たとえば、`return View("Test")` は */Views/Home/Test.cshtml* だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="eb466-360">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="eb466-361">プリコンパイル済みのビュー: ASP.NET Core 2.0 以降では、プリコンパイル済みのビューの参照は、すべてのオペレーティング システムで大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="eb466-361">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="eb466-362">動作は、Windows での物理ファイル プロバイダーの動作と同じです。</span><span class="sxs-lookup"><span data-stu-id="eb466-362">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="eb466-363">2 つのプリコンパイル済みビューの相違点が大文字と小文字の使い分けだけの場合、参照の結果はどちらになるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="eb466-363">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="eb466-364">開発者には、ファイル名とディレクトリ名の大文字/小文字の使い分けを、次のものと一致させることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="eb466-364">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="eb466-365">領域、コントローラー、アクションの名前。</span><span class="sxs-lookup"><span data-stu-id="eb466-365">Area, controller, and action names.</span></span>
* <span data-ttu-id="eb466-366">Razor トピック.</span><span class="sxs-lookup"><span data-stu-id="eb466-366">Razor Pages.</span></span>

<span data-ttu-id="eb466-367">大文字と小文字の使い分けを一致させると、展開は基になっているファイル システムに関係なくビューを検索できます。</span><span class="sxs-lookup"><span data-stu-id="eb466-367">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb466-368">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eb466-368">Additional resources</span></span>

<span data-ttu-id="eb466-369">を[使用した ASP.NET Web プログラミング Razor の概要構文には、](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)構文を使用したプログラミングの多くのサンプルが用意されて Razor います。</span><span class="sxs-lookup"><span data-stu-id="eb466-369">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
