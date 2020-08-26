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
ms.openlocfilehash: e42ee7a80475e784ea4791395d9b0665aca94a03
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865168"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="d3935-103">Razor ASP.NET Core の構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="d3935-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="d3935-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Taylor Mullen](https://twitter.com/ntaylormullen)、 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="d3935-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="d3935-105">Razor は、web ページにサーバーベースのコードを埋め込むためのマークアップ構文です。</span><span class="sxs-lookup"><span data-stu-id="d3935-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="d3935-106">構文は、 Razor Razor マークアップ、C#、および HTML で構成されています。</span><span class="sxs-lookup"><span data-stu-id="d3935-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="d3935-107">に含まれるファイル Razor に *.cshtml*は、通常、拡張子が付いています。</span><span class="sxs-lookup"><span data-stu-id="d3935-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="d3935-108">Razorは、 [ Razor コンポーネント](xref:blazor/components/index)ファイル (*razor*) にもあります。</span><span class="sxs-lookup"><span data-stu-id="d3935-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="d3935-109">HTML のレンダリング</span><span class="sxs-lookup"><span data-stu-id="d3935-109">Rendering HTML</span></span>

<span data-ttu-id="d3935-110">既定の Razor 言語は HTML です。</span><span class="sxs-lookup"><span data-stu-id="d3935-110">The default Razor language is HTML.</span></span> <span data-ttu-id="d3935-111">マークアップから html をレンダリング Razor することは、html ファイルから html をレンダリングする場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="d3935-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="d3935-112">ファイル *内の*HTML マークアップ Razor は、サーバーによって変更されずに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="d3935-113">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="d3935-113">Razor syntax</span></span>

<span data-ttu-id="d3935-114">Razor C# をサポートし、シンボルを使用して `@` HTML から c# に移行します。</span><span class="sxs-lookup"><span data-stu-id="d3935-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="d3935-115">Razor C# の式を評価し、HTML 出力に表示します。</span><span class="sxs-lookup"><span data-stu-id="d3935-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="d3935-116">記号の `@` 後に[ Razor 予約済みのキーワード](#razor-reserved-keywords)がある場合は、 Razor 固有のマークアップに遷移します。</span><span class="sxs-lookup"><span data-stu-id="d3935-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="d3935-117">それ以外の場合は、普通の C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="d3935-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="d3935-118">マークアップでシンボルをエスケープするには `@` Razor 、2番目のシンボルを使用し `@` ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="d3935-119">HTML では、コードは 1 つの `@` 記号でレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="d3935-120">メール アドレスを含む HTML の属性とコンテンツは、`@` 記号を遷移文字として扱いません。</span><span class="sxs-lookup"><span data-stu-id="d3935-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="d3935-121">次の例の電子メールアドレスは、解析によってそのまま残り Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="d3935-122">暗黙的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="d3935-122">Implicit Razor expressions</span></span>

<span data-ttu-id="d3935-123">暗黙的 Razor な式の先頭に `@` は、次の C# コードが続きます。</span><span class="sxs-lookup"><span data-stu-id="d3935-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="d3935-124">C# の `await` キーワードを除き、暗黙的な式にスペースを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="d3935-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="d3935-125">C# のステートメントに明確な終わりがある場合は、スペースを混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="d3935-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="d3935-126">暗黙的な式では、山かっこ (`<>`) の内側の文字は HTML タグとして解釈されるため、C# ジェネリックを含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="d3935-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="d3935-127">次のコードは有効では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="d3935-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="d3935-128">上記のコードでは、次のいずれかのようなコンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="d3935-129">The "int" element wasn't closed.</span><span class="sxs-lookup"><span data-stu-id="d3935-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="d3935-130">All elements must be either self-closing or have a matching end tag. ("int" 要素が閉じられませんでした。すべての要素は、自己終了するか、対応する終了タグが存在する必要があります。)</span><span class="sxs-lookup"><span data-stu-id="d3935-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="d3935-131">メソッド グループ 'GenericMethod' を非デリゲート型 'object' に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="d3935-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="d3935-132">このメソッドを呼び出しますか?</span><span class="sxs-lookup"><span data-stu-id="d3935-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="d3935-133">ジェネリックメソッドの呼び出しは、[明示的な Razor 式](#explicit-razor-expressions)または[ Razor コードブロック](#razor-code-blocks)でラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="d3935-134">明示的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="d3935-134">Explicit Razor expressions</span></span>

<span data-ttu-id="d3935-135">明示的な Razor 式は、 `@` 均衡かっこ付きの記号で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="d3935-136">先週の時間を表示するには、次の Razor マークアップを使用します。</span><span class="sxs-lookup"><span data-stu-id="d3935-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="d3935-137">`@()` のかっこ内の内容が評価されて、出力にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="d3935-138">前のセクションで説明した暗黙的な式は、一般に、スペースを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="d3935-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="d3935-139">次のコードでは、現在時刻から 1 週間は減算されません。</span><span class="sxs-lookup"><span data-stu-id="d3935-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="d3935-140">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="d3935-141">明示的な式を使うと、テキストと式の結果を連結できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="d3935-142">明示的な式を使わないと、`<p>Age@joe.Age</p>` はメール アドレスとして扱われ、`<p>Age@joe.Age</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="d3935-143">明示的な式として記述すると、`<p>Age33</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="d3935-144">明示的な式を使うと、ジェネリック メソッドから *.cshtml* ファイルに出力できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="d3935-145">次のマークアップは、C# ジェネリックの山かっこによって発生した前述のエラーを修正する方法について示します。</span><span class="sxs-lookup"><span data-stu-id="d3935-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="d3935-146">コードは明示的な式として書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d3935-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="d3935-147">式のエンコード</span><span class="sxs-lookup"><span data-stu-id="d3935-147">Expression encoding</span></span>

<span data-ttu-id="d3935-148">文字列として評価される C# の式は、HTML でエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="d3935-149">`IHtmlContent` として評価される C# の式は、`IHtmlContent.WriteTo` によって直接レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="d3935-150">`IHtmlContent` として評価されない C# の式は、`ToString` によって文字列に変換され、レンダリングされる前にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="d3935-151">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="d3935-152">HTML はブラウザーで次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="d3935-153">`HtmlHelper.Raw` の出力はエンコードされませんが、HTML マークアップとしてレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="d3935-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="d3935-154">サニタイズされていないユーザー入力で `HtmlHelper.Raw` を使うと、セキュリティ上のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="d3935-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="d3935-155">ユーザー入力には、悪意のある JavaScript または他の攻撃が含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="d3935-156">ユーザー入力をサニタイズすることは困難です。</span><span class="sxs-lookup"><span data-stu-id="d3935-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="d3935-157">ユーザー入力では `HtmlHelper.Raw` を使わないでください。</span><span class="sxs-lookup"><span data-stu-id="d3935-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="d3935-158">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="d3935-159">Razor コードブロック</span><span class="sxs-lookup"><span data-stu-id="d3935-159">Razor code blocks</span></span>

<span data-ttu-id="d3935-160">Razor コードブロックはで始まり `@` 、で囲まれて `{}` います。</span><span class="sxs-lookup"><span data-stu-id="d3935-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="d3935-161">式とは異なり、コード ブロック内の C# コードはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="d3935-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="d3935-162">ビュー内のコード ブロックと式は同じスコープを共有し、次の順序で定義されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="d3935-163">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3935-164">コード ブロックで、る[ローカル関数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)をマークアップで宣言し、テンプレート メソッドとしてのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d3935-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="d3935-165">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="d3935-166">暗黙の移行</span><span class="sxs-lookup"><span data-stu-id="d3935-166">Implicit transitions</span></span>

<span data-ttu-id="d3935-167">コードブロックの既定の言語は C# ですが、 Razor ページは HTML に戻ることができます。</span><span class="sxs-lookup"><span data-stu-id="d3935-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="d3935-168">明示的に区切られた遷移</span><span class="sxs-lookup"><span data-stu-id="d3935-168">Explicit delimited transition</span></span>

<span data-ttu-id="d3935-169">HTML を表示する必要があるコードブロックのサブセクションを定義するには、タグを使用して、表示する文字を囲み Razor `<text>` ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="d3935-170">HTML タグによって囲まれていない HTML をレンダリングするには、この方法を使います。</span><span class="sxs-lookup"><span data-stu-id="d3935-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="d3935-171">HTML またはタグがないと Razor 、 Razor ランタイムエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="d3935-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="d3935-172">`<text>` タグは、内容をレンダリングするときに空白文字を制御するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="d3935-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="d3935-173">`<text>` タグの間の内容だけがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="d3935-174">`<text>` タグの前後にある空白文字は HTML の出力には表示されません。</span><span class="sxs-lookup"><span data-stu-id="d3935-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="d3935-175">明示的な行の遷移</span><span class="sxs-lookup"><span data-stu-id="d3935-175">Explicit line transition</span></span>

<span data-ttu-id="d3935-176">残りの行全体をコード ブロック内に HTML としてレンダリングするには、`@:` 構文を使います。</span><span class="sxs-lookup"><span data-stu-id="d3935-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="d3935-177">コードにを指定しないと `@:` 、 Razor ランタイムエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="d3935-178">`@`ファイルに余分な文字が含まれ Razor ていると、ステートメントの後のブロックで、コンパイラエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="d3935-179">これらのコンパイラ エラーは、実際のエラーは報告されたエラーより前で発生しているため、理解するのが難しい場合があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="d3935-180">このエラーは、複数の暗黙的/明示的な式を 1 つのコード ブロックに結合した後で発生することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="d3935-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="d3935-181">制御構造</span><span class="sxs-lookup"><span data-stu-id="d3935-181">Control structures</span></span>

<span data-ttu-id="d3935-182">制御構造は、コード ブロックの拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="d3935-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="d3935-183">コード ブロックのすべての側面 (マークアップへの遷移、インライン C# ) が、次の構造にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="d3935-184">条件 `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="d3935-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="d3935-185">`@if` は、いつコードを実行するかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d3935-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="d3935-186">`else` および `else if` には、`@` 記号は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d3935-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="d3935-187">次のマークアップでは、switch ステートメントの使い方を示します。</span><span class="sxs-lookup"><span data-stu-id="d3935-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="d3935-188">サウンド `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="d3935-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="d3935-189">ループ制御ステートメントを使って、テンプレート化された HTML をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="d3935-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="d3935-190">人の一覧をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="d3935-190">To render a list of people:</span></span>

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

<span data-ttu-id="d3935-191">以下のループ ステートメントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d3935-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="d3935-192">複合 `@using`</span><span class="sxs-lookup"><span data-stu-id="d3935-192">Compound `@using`</span></span>

<span data-ttu-id="d3935-193">C# では、オブジェクトを確実に破棄するために `using` オブジェクトが使われています。</span><span class="sxs-lookup"><span data-stu-id="d3935-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="d3935-194">で Razor は、同じメカニズムを使用して、追加のコンテンツを含む HTML ヘルパーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d3935-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="d3935-195">次のコードの HTML ヘルパーは、`@using` ステートメントを含む `<form>` タグをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d3935-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="d3935-196">例外処理は C# に似ています。</span><span class="sxs-lookup"><span data-stu-id="d3935-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="d3935-197">Razor には、次のように、重要なセクションを lock ステートメントで保護する機能があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-197">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="d3935-198">説明</span><span class="sxs-lookup"><span data-stu-id="d3935-198">Comments</span></span>

<span data-ttu-id="d3935-199">Razor C# および HTML コメントをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d3935-199">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="d3935-200">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="d3935-201">Razor コメントは、web ページが表示される前にサーバーによって削除されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-201">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="d3935-202">Razor は `@*  *@` 、を使用してコメントを区切ります。</span><span class="sxs-lookup"><span data-stu-id="d3935-202">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="d3935-203">次のコードはコメント化されているため、サーバーはどのマークアップもレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="d3935-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="d3935-204">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="d3935-204">Directives</span></span>

<span data-ttu-id="d3935-205">Razor ディレクティブは、記号の後に予約済みのキーワードを持つ暗黙的な式で表され `@` ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-205">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="d3935-206">通常、ディレクティブは、ビューの解析方法を変更したり、異なる機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="d3935-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="d3935-207">が Razor ビューのコードを生成する方法を理解すると、ディレクティブのしくみを理解しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="d3935-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="d3935-208">上のコードでは、次のようなクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-208">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="d3935-209">この記事の後半で、「 [ Razor ビューに対して生成された C# クラスを調べる](#inspect-the-razor-c-class-generated-for-a-view) 」セクションでは、この生成されたクラスを表示する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d3935-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="d3935-210">`@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="d3935-211">次の例では、`[Authorize]` 属性が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="d3935-212">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-213">この `@code` ブロックにより、 [ Razor コンポーネント](xref:blazor/components/index)は、コンポーネントに C# のメンバー (フィールド、プロパティ、およびメソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-213">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="d3935-214">コンポーネントの場合 Razor 、 `@code` はのエイリアスであり、よりも優先され [`@functions`](#functions) `@functions` ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="d3935-215">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="d3935-216">`@functions` ディレクティブでは、生成されたクラスに C# メンバー (フィールド、プロパティ、メソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3935-217">[ [ Razor コンポーネント](xref:blazor/components/index)] で、を使用して `@code` `@functions` C# メンバーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d3935-217">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="d3935-218">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d3935-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="d3935-219">このコードは、次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="d3935-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="d3935-220">次のコードは、生成された Razor C# クラスです。</span><span class="sxs-lookup"><span data-stu-id="d3935-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3935-221">`@functions` メソッドは、マークアップが与えられているとき、テンプレート メソッドとしてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d3935-221">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="d3935-222">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="d3935-223">`@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="d3935-224">次の例では、<xref:System.IDisposable.Dispose*> メソッドを呼び出せるように、<xref:System.IDisposable?displayProperty=fullName> が実装されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="d3935-225">`@inherits` ディレクティブは、ビューが継承するクラスの完全な制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="d3935-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="d3935-226">次のコードは、カスタム Razor ページの種類です。</span><span class="sxs-lookup"><span data-stu-id="d3935-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="d3935-227">`CustomText` がビューに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="d3935-228">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="d3935-229">`@model` と `@inherits` は同じビューで使うことができます。</span><span class="sxs-lookup"><span data-stu-id="d3935-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="d3935-230">`@inherits` は、ビューがインポートする *_ViewImports.cshtml* ファイルで指定できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="d3935-231">次のコードは、厳密に型指定されたビューの例です。</span><span class="sxs-lookup"><span data-stu-id="d3935-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="d3935-232">モデルに rick@contoso.com が渡された場合、ビューは次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="d3935-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="d3935-233">ディレクティブを使用すると、サービス `@inject` Razor [コンテナー](xref:fundamentals/dependency-injection) からビューにサービスを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="d3935-234">詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d3935-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="d3935-235">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-236">ディレクティブは、 `@layout` コンポーネントのレイアウトを指定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="d3935-237">レイアウト コンポーネントは、コードの重複や不整合を回避するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="d3935-238">詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="d3935-239">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d3935-240">`@model` ディレクティブにより、ビューまたはページに渡されるモデルの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="d3935-241">Razor個々のユーザーアカウントで作成された ASP.NET CORE MVC またはページアプリでは、 *Views/Account/Login. cshtml*には次のモデル宣言が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d3935-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="d3935-242">生成されるクラスは、`RazorPage<dynamic>` を継承します。</span><span class="sxs-lookup"><span data-stu-id="d3935-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="d3935-243">Razor`Model`ビューに渡されるモデルにアクセスするためのプロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="d3935-243">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="d3935-244">`@model` ディレクティブにより、`Model` プロパティの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="d3935-245">ディレクティブでは、ビューが派生する生成されたクラスの `T` を `RazorPage<T>` で指定します。</span><span class="sxs-lookup"><span data-stu-id="d3935-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="d3935-246">`@model` ディレクティブが指定されていない場合、`Model` プロパティは `dynamic` 型になります。</span><span class="sxs-lookup"><span data-stu-id="d3935-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="d3935-247">詳細については、「 [厳密に型指定されたモデルと @model キーワード](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="d3935-248">`@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="d3935-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="d3935-249">生成された Razor ページ、MVC ビュー、またはコンポーネントのクラスの名前空間を設定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="d3935-250">ページ、ビュー、またはコンポーネントクラスのルート派生名前空間を、ディレクトリツリー内の最も近いインポートファイル、 *_ViewImports* (ビューまたはページ)、または *_Imports razor* ( Razor コンポーネント) に設定します。</span><span class="sxs-lookup"><span data-stu-id="d3935-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="d3935-251">次の表に示すページの例については、「」を参照して Razor ください。</span><span class="sxs-lookup"><span data-stu-id="d3935-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="d3935-252">各ページで *Pages/_ViewImports.cshtml* がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="d3935-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="d3935-253">*Pages/_ViewImports.cshtml* に `@namespace Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d3935-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="d3935-254">各ページには、その名前空間のルートとして `Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d3935-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="d3935-255">Page</span><span class="sxs-lookup"><span data-stu-id="d3935-255">Page</span></span>                                        | <span data-ttu-id="d3935-256">名前空間</span><span class="sxs-lookup"><span data-stu-id="d3935-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="d3935-257">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="d3935-258">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="d3935-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="d3935-260">前のリレーションシップは、MVC ビューおよびコンポーネントで使用されるファイルのインポートに適用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="d3935-261">複数のインポート ファイルに `@namespace` ディレクティブがあるとき、ディレクトリ ツリーでページ、ビュー、またはコンポーネントに最も近いファイルがルート名前空間の設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="d3935-262">前の例の *EvenMorePages* フォルダーに `@namespace Another.Planet` が含まれるインポート ファイルがある場合 (または、*Pages/MorePages/EvenMorePages/Page.cshtml* ファイルに `@namespace Another.Planet` が含まれる場合)、結果は次の表のようになります。</span><span class="sxs-lookup"><span data-stu-id="d3935-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="d3935-263">Page</span><span class="sxs-lookup"><span data-stu-id="d3935-263">Page</span></span>                                        | <span data-ttu-id="d3935-264">名前空間</span><span class="sxs-lookup"><span data-stu-id="d3935-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="d3935-265">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="d3935-266">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="d3935-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d3935-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3935-268">`@page` ディレクティブには、それが表示されるファイルの型によって、さまざまな効果があります。</span><span class="sxs-lookup"><span data-stu-id="d3935-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="d3935-269">ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="d3935-269">The directive:</span></span>

* <span data-ttu-id="d3935-270">は、ファイルがページであることを示し*ます。* Razor</span><span class="sxs-lookup"><span data-stu-id="d3935-270">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="d3935-271">詳細については、「[カスタム ルート](xref:razor-pages/index#custom-routes)」と「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="d3935-272">コンポーネントが要求を直接処理する必要があることを指定し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="d3935-273">詳細については、「<xref:blazor/fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-273">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3935-274">`@page`ファイルの先頭行にあるディレクティブは、ファイルがページであることを示し*ます。* Razor</span><span class="sxs-lookup"><span data-stu-id="d3935-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="d3935-275">詳細については、「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="d3935-276">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d3935-277">ディレクティブは、 `@section` ビューまたはページが HTML ページのさまざまな部分でコンテンツを表示できるようにするために、 [MVC および Razor ページレイアウト](xref:mvc/views/layout) と組み合わせて使用されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="d3935-278">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="d3935-279">`@using` ディレクティブは、生成されるビューに C# の `using` ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="d3935-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3935-280">[ Razor コンポーネント](xref:blazor/components/index)で `@using` は、スコープ内のコンポーネントも制御します。</span><span class="sxs-lookup"><span data-stu-id="d3935-280">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="d3935-281">ディレクティブ属性</span><span class="sxs-lookup"><span data-stu-id="d3935-281">Directive attributes</span></span>

<span data-ttu-id="d3935-282">Razor ディレクティブ属性は、記号の後に予約済みのキーワードを使用した暗黙的な式によって表され `@` ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-282">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="d3935-283">通常、ディレクティブ属性は、要素の解析方法を変更したり、さまざまな機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="d3935-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="d3935-284">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-285">`@attributes` では、非宣言属性のレンダリングがコンポーネントに許可されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="d3935-286">詳細については、「<xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-286">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="d3935-287">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-288">コンポーネントのデータ バインドは、`@bind` 属性によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="d3935-289">詳細については、「<xref:blazor/components/data-binding>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-289">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="d3935-290">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-290">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-291">Razor コンポーネントのイベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="d3935-291">Razor provides event handling features for components.</span></span> <span data-ttu-id="d3935-292">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-292">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="d3935-293">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-294">イベントの既定のアクションを禁止します。</span><span class="sxs-lookup"><span data-stu-id="d3935-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="d3935-295">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-296">イベントのイベント伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="d3935-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="d3935-297">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-298">`@key` ディレクティブ属性により、コンポーネントの比較アルゴリズムは、キーの値に基づいて要素またはコンポーネントの保存を保証します。</span><span class="sxs-lookup"><span data-stu-id="d3935-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="d3935-299">詳細については、「<xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-299">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="d3935-300">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-301">コンポーネント参照 (`@ref`) からは、コンポーネント インスタンスにコマンドを発行できるように、そのインスタンスを参照する方法が与えられます。</span><span class="sxs-lookup"><span data-stu-id="d3935-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="d3935-302">詳細については、「<xref:blazor/components/index#capture-references-to-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-302">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="d3935-303">*このシナリオは、 Razor コンポーネント (razor) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d3935-304">`@typeparam` ディレクティブによって、生成されるコンポーネント クラスのジェネリック型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="d3935-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="d3935-305">詳細については、「<xref:blazor/components/templated-components#generic-typed-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d3935-305">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="d3935-306">テンプレート化 Razor デリゲート</span><span class="sxs-lookup"><span data-stu-id="d3935-306">Templated Razor delegates</span></span>

<span data-ttu-id="d3935-307">Razor テンプレートを使用すると、次の形式で UI スニペットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-307">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="d3935-308">次の例は、テンプレート化されたデリゲートをとして指定する方法を示してい Razor <xref:System.Func%602> ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="d3935-309">デリゲートによってカプセル化されるメソッドのパラメーターに対しては、[dynamic 型](/dotnet/csharp/programming-guide/types/using-type-dynamic)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d3935-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="d3935-310">デリゲートの戻り値としては、[object 型](/dotnet/csharp/language-reference/keywords/object)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d3935-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="d3935-311">テンプレートは、`Name` プロパティを持つ `Pet` の <xref:System.Collections.Generic.List%601> で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="d3935-312">テンプレートは、`foreach` ステートメントによって提供される `pets` で表示されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="d3935-313">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="d3935-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="d3935-314">Razorメソッドの引数としてインラインテンプレートを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d3935-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="d3935-315">次の例では、 `Repeat` メソッドがテンプレートを受け取り Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="d3935-316">メソッドは、テンプレートを使用して、リストから提供される項目の繰り返しで HTML コンテンツを生成します。</span><span class="sxs-lookup"><span data-stu-id="d3935-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="d3935-317">前の例のペットのリストを使用して、次のように `Repeat` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d3935-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="d3935-318"><xref:System.Collections.Generic.List%601> の `Pet`。</span><span class="sxs-lookup"><span data-stu-id="d3935-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="d3935-319">各ペットを繰り返す回数。</span><span class="sxs-lookup"><span data-stu-id="d3935-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="d3935-320">順不同のリストのリスト項目に対して使用するインライン テンプレート。</span><span class="sxs-lookup"><span data-stu-id="d3935-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="d3935-321">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="d3935-321">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="d3935-322">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="d3935-322">Tag Helpers</span></span>

<span data-ttu-id="d3935-323">*このシナリオは、MVC ビューおよび Razor ページ (cshtml) にのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="d3935-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d3935-324">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)に関する 3 つのディレクティブがあります。</span><span class="sxs-lookup"><span data-stu-id="d3935-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="d3935-325">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="d3935-325">Directive</span></span> | <span data-ttu-id="d3935-326">関数</span><span class="sxs-lookup"><span data-stu-id="d3935-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="d3935-327">ビューでタグ ヘルパーを使えるようにします。</span><span class="sxs-lookup"><span data-stu-id="d3935-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="d3935-328">前に追加したタグ ヘルパーをビューから削除します。</span><span class="sxs-lookup"><span data-stu-id="d3935-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="d3935-329">タグ プレフィックスを指定して、タグ ヘルパーのサポートを有効にしたり、タグ ヘルパーの使用を明示的にしたりします。</span><span class="sxs-lookup"><span data-stu-id="d3935-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="d3935-330">Razor 予約済みキーワード</span><span class="sxs-lookup"><span data-stu-id="d3935-330">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="d3935-331">Razor keywords</span><span class="sxs-lookup"><span data-stu-id="d3935-331">Razor keywords</span></span>

* <span data-ttu-id="d3935-332">`page` (ASP.NET Core 2.1 以降が必要)</span><span class="sxs-lookup"><span data-stu-id="d3935-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="d3935-333">`helper` (現在 ASP.NET Core ではサポートされていません)</span><span class="sxs-lookup"><span data-stu-id="d3935-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="d3935-334">Razor キーワードは、でエスケープされ `@(Razor Keyword)` ます (たとえば、 `@(functions)` )。</span><span class="sxs-lookup"><span data-stu-id="d3935-334">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="d3935-335">C# の Razor キーワード</span><span class="sxs-lookup"><span data-stu-id="d3935-335">C# Razor keywords</span></span>

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

<span data-ttu-id="d3935-336">C# の Razor キーワードは、を使用してダブルエスケープする必要があり `@(@C# Razor Keyword)` ます (例: `@(@case)` )。</span><span class="sxs-lookup"><span data-stu-id="d3935-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="d3935-337">最初のは、 `@` パーサーをエスケープし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="d3935-338">2 番目の `@` は、C# パーサーをエスケープします。</span><span class="sxs-lookup"><span data-stu-id="d3935-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="d3935-339">予約済みキーワードがで使用されていません Razor</span><span class="sxs-lookup"><span data-stu-id="d3935-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="d3935-340">Razorビューに対して生成された C# クラスを検査する</span><span class="sxs-lookup"><span data-stu-id="d3935-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d3935-341">.NET Core SDK 2.1 以降では、 [ Razor SDK](xref:razor-pages/sdk)によってファイルのコンパイルが処理さ Razor れます。</span><span class="sxs-lookup"><span data-stu-id="d3935-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="d3935-342">プロジェクトをビルドすると、SDK によって、 Razor プロジェクトルートに*obj/<build_configuration> Razor /<target_framework_moniker/* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="d3935-343">ディレクトリ内のディレクトリ構造は、 *Razor* プロジェクトのディレクトリ構造をミラー化します。</span><span class="sxs-lookup"><span data-stu-id="d3935-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="d3935-344">.NET Core 2.1 を対象とする ASP.NET Core 2.1 ページプロジェクトでは、次のディレクトリ構造について考えてみ Razor ます。</span><span class="sxs-lookup"><span data-stu-id="d3935-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="d3935-345">*Debug* 構成でプロジェクトを作成すると、次の *obj* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="d3935-346">*Pages/Index. cshtml*の生成されたクラスを表示するには、 *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*を開きます。</span><span class="sxs-lookup"><span data-stu-id="d3935-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="d3935-347">次のクラスを ASP.NET Core MVC プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="d3935-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="d3935-348">`Startup.ConfigureServices` で、MVC によって追加された `RazorTemplateEngine` を `CustomTemplateEngine` クラスでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d3935-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="d3935-349">`CustomTemplateEngine` の `return csharpDocument;` ステートメントにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="d3935-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="d3935-350">プログラムの実行がブレークポイントで停止したら、`generatedCode` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="d3935-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode のテキスト ビジュアライザーの表示](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="d3935-352">ビューの参照と大文字/小文字の区別</span><span class="sxs-lookup"><span data-stu-id="d3935-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="d3935-353">Razorビューエンジンは、ビューに対して大文字と小文字を区別して検索を実行します。</span><span class="sxs-lookup"><span data-stu-id="d3935-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="d3935-354">ただし、実際の参照は、基になるファイル システムによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="d3935-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="d3935-355">ファイル ベースのソース:</span><span class="sxs-lookup"><span data-stu-id="d3935-355">File based source:</span></span>
  * <span data-ttu-id="d3935-356">大文字と小文字が区別されないファイル システムを使っているオペレーティング システム (Windows など) では、物理的なファイル プロバイダーの参照は大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="d3935-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="d3935-357">たとえば、`return View("Test")` は、*/Views/Home/Test.cshtml*、*/Views/home/test.cshtml*、その他のすべての大文字と小文字のバリエーションと一致します。</span><span class="sxs-lookup"><span data-stu-id="d3935-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="d3935-358">大文字と小文字が区別されるファイル システム (たとえば、Linux、OSX、および `EmbeddedFileProvider`) では、参照は大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="d3935-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="d3935-359">たとえば、`return View("Test")` は */Views/Home/Test.cshtml* だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="d3935-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="d3935-360">プリコンパイル済みのビュー: ASP.NET Core 2.0 以降では、プリコンパイル済みのビューの参照は、すべてのオペレーティング システムで大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="d3935-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="d3935-361">動作は、Windows での物理ファイル プロバイダーの動作と同じです。</span><span class="sxs-lookup"><span data-stu-id="d3935-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="d3935-362">2 つのプリコンパイル済みビューの相違点が大文字と小文字の使い分けだけの場合、参照の結果はどちらになるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="d3935-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="d3935-363">開発者には、ファイル名とディレクトリ名の大文字/小文字の使い分けを、次のものと一致させることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d3935-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="d3935-364">領域、コントローラー、アクションの名前。</span><span class="sxs-lookup"><span data-stu-id="d3935-364">Area, controller, and action names.</span></span>
* <span data-ttu-id="d3935-365">Razor トピック.</span><span class="sxs-lookup"><span data-stu-id="d3935-365">Razor Pages.</span></span>

<span data-ttu-id="d3935-366">大文字と小文字の使い分けを一致させると、展開は基になっているファイル システムに関係なくビューを検索できます。</span><span class="sxs-lookup"><span data-stu-id="d3935-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3935-367">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d3935-367">Additional resources</span></span>

<span data-ttu-id="d3935-368">を[使用した ASP.NET Web プログラミング Razor の概要構文には、](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)構文を使用したプログラミングの多くのサンプルが用意されて Razor います。</span><span class="sxs-lookup"><span data-stu-id="d3935-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
