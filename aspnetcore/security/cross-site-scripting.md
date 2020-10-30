---
title: ASP.NET Core でクロスサイトスクリプティング (XSS) を防止する
author: rick-anderson
description: クロスサイトスクリプティング (XSS) と、ASP.NET Core アプリでこの脆弱性に対処するための手法について説明します。
ms.author: riande
ms.date: 10/02/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053086"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="8785c-103">ASP.NET Core でクロスサイトスクリプティング (XSS) を防止する</span><span class="sxs-lookup"><span data-stu-id="8785c-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="8785c-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8785c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8785c-105">クロスサイトスクリプティング (XSS) はセキュリティの脆弱性であり、攻撃者がクライアント側のスクリプト (通常は JavaScript) を web ページに配置できるようにします。</span><span class="sxs-lookup"><span data-stu-id="8785c-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="8785c-106">他のユーザーが影響を受けたページを読み込むと、攻撃者のスクリプトが実行され、攻撃者が s およびセッショントークンを盗むことができ :::no-loc(cookie)::: ます。また、DOM 操作を通じて web ページの内容を変更したり、ブラウザーを別のページにリダイレクトしたりすることができます。</span><span class="sxs-lookup"><span data-stu-id="8785c-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal :::no-loc(cookie):::s and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="8785c-107">XSS 脆弱性は一般的に、アプリケーションがユーザー入力を受け取り、それを検証、エンコード、またはエスケープせずにページに出力するときに発生します。</span><span class="sxs-lookup"><span data-stu-id="8785c-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="8785c-108">XSS からアプリケーションを保護する</span><span class="sxs-lookup"><span data-stu-id="8785c-108">Protecting your application against XSS</span></span>

<span data-ttu-id="8785c-109">基本的なレベルの XSS では、アプリケーションを欺いして、 `<script>` レンダリングされたページにタグを挿入したり、イベントを要素に挿入したりし `On*` ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="8785c-110">開発者は、次の防止手順を使用して、アプリケーションに XSS が導入されないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="8785c-111">次の手順を実行しない限り、信頼されていないデータを HTML 入力に入れないでください。</span><span class="sxs-lookup"><span data-stu-id="8785c-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="8785c-112">信頼されていないデータとは、攻撃者によって制御される可能性があるすべてのデータのことです。攻撃者としてデータベースからデータをソース化したとしても、アプリケーションを侵害できない場合でも、データベースを侵害する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="8785c-113">HTML 要素内に信頼できないデータを配置する前に、html がエンコードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8785c-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="8785c-114">HTML エンコーディングはなど &lt; の文字を受け取り、lt; のように安全な形式に変更し &amp; ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="8785c-115">信頼できないデータを HTML 属性に配置する前に、HTML がエンコードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8785c-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="8785c-116">HTML 属性のエンコードは、HTML エンコーディングのスーパーセットであり、"and" などの追加の文字をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="8785c-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="8785c-117">信頼されていないデータを JavaScript に配置する前に、実行時に取得する内容を持つ HTML 要素にデータを配置します。</span><span class="sxs-lookup"><span data-stu-id="8785c-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="8785c-118">これが不可能な場合は、データが JavaScript でエンコードされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8785c-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="8785c-119">Javascript エンコードでは、JavaScript では危険な文字が使用され、その16進数で置き換えら &lt; れます。たとえば、としてエンコードさ `\u003C` れます。</span><span class="sxs-lookup"><span data-stu-id="8785c-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="8785c-120">信頼されていないデータを URL クエリ文字列に含める前に、URL がエンコードされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="8785c-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="8785c-121">を使用した HTML エンコード :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="8785c-121">HTML Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="8785c-122">:::no-loc(Razor):::MVC で使用されるエンジンでは、そのような処理を回避することが難しい場合を除き、変数からのすべての出力ソースが自動的にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="8785c-122">The :::no-loc(Razor)::: engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="8785c-123">ディレクティブを使用すると、常に HTML 属性のエンコード規則が使用さ *@* れます。</span><span class="sxs-lookup"><span data-stu-id="8785c-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="8785c-124">Html 属性のエンコードは HTML エンコーディングのスーパーセットなので、HTML エンコーディングと HTML 属性のどちらを使用する必要があるかについては、それを気にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8785c-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="8785c-125">信頼されていない入力を JavaScript に直接挿入しようとする場合は、HTML コンテキストでのみ @ を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="8785c-126">タグヘルパーでは、タグパラメーターで使用する入力もエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="8785c-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="8785c-127">次のビューを実行し :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-127">Take the following :::no-loc(Razor)::: view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="8785c-128">このビューは、 *Untrustedinput* 変数の内容を出力します。</span><span class="sxs-lookup"><span data-stu-id="8785c-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="8785c-129">この変数には、XSS 攻撃で使用されるいくつかの文字が含まれてい &lt; &gt; ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="8785c-130">ソースを調べると、次のようにエンコードされた出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="8785c-131">MVC ASP.NET Core は、 `HtmlString` 出力時に自動的にエンコードされないクラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8785c-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="8785c-132">これは XSS 脆弱性を公開するため、信頼されていない入力と組み合わせて使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8785c-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="8785c-133">を使用した JavaScript のエンコード :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="8785c-133">JavaScript Encoding using :::no-loc(Razor):::</span></span>

<span data-ttu-id="8785c-134">JavaScript に値を挿入して、ビューで処理することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="8785c-135">これには、2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-135">There are two ways to do this.</span></span> <span data-ttu-id="8785c-136">値を挿入する最も安全な方法は、タグのデータ属性に値を配置し、JavaScript で値を取得することです。</span><span class="sxs-lookup"><span data-stu-id="8785c-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="8785c-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8785c-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="8785c-138">上記のマークアップでは、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="8785c-139">上記のコードでは、次の出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="8785c-140">DOM 要素を作成したり、動的に生成されたコンテンツを使用したりするために、JavaScript で信頼 **されていない** 入力を連結しないように `document.write()` します。</span><span class="sxs-lookup"><span data-stu-id="8785c-140">Do \* **NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="8785c-141">次のいずれかの方法を使用して、コードが DOM ベースの XSS: _ に公開されないように `createElement()` し、適切なメソッドまたはノードなどのプロパティを使用してプロパティ値を割り当て `node.textContent=` ます。InnerText = '。</span><span class="sxs-lookup"><span data-stu-id="8785c-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="8785c-142">`document.CreateTextNode()` 適切な DOM の場所に追加します。</span><span class="sxs-lookup"><span data-stu-id="8785c-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="8785c-143">コード内のエンコーダーへのアクセス</span><span class="sxs-lookup"><span data-stu-id="8785c-143">Accessing encoders in code</span></span>

<span data-ttu-id="8785c-144">HTML、JavaScript、および URL エンコーダーは、次の2つの方法でコードで使用できます。 [依存関係の挿入](xref:fundamentals/dependency-injection) を使用して挿入することも、名前空間に含まれる既定のエンコーダーを使用することもでき `System.Text.Encodings.Web` ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="8785c-145">既定のエンコーダーを使用する場合、安全として扱う文字範囲に適用したものは有効になりません。既定のエンコーダーでは、可能な限り安全なエンコーディング規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="8785c-146">DI を使用して構成可能なエンコーダーを使用するには、コンストラクターが *htmlencoder* 、 *JavaScriptEncoder* 、および *urlencoder* パラメーターを必要に応じて受け取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder* , *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="8785c-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8785c-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="8785c-148">エンコード URL パラメーター</span><span class="sxs-lookup"><span data-stu-id="8785c-148">Encoding URL Parameters</span></span>

<span data-ttu-id="8785c-149">信頼できない入力を含む URL クエリ文字列を値として作成する場合は、を使用して `UrlEncoder` 値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="8785c-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="8785c-150">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="8785c-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="8785c-151">エンコード後、エンコード値の変数にはが含まれ `%22Quoted%20Value%20with%20spaces%20and%20%26%22` ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="8785c-152">スペース、引用符、句読点、およびその他の安全でない文字は、16進数値にパーセントでエンコードされます。たとえば、スペース文字は %20 になります。</span><span class="sxs-lookup"><span data-stu-id="8785c-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="8785c-153">URL パスの一部として信頼されていない入力は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8785c-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="8785c-154">常に信頼できない入力をクエリ文字列値として渡します。</span><span class="sxs-lookup"><span data-stu-id="8785c-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="8785c-155">エンコーダーのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="8785c-155">Customizing the Encoders</span></span>

<span data-ttu-id="8785c-156">既定では、エンコーダーは基本的なラテン Unicode 範囲に限定されたセーフリストを使用し、その範囲外のすべての文字を同等の文字コードとしてエンコードします。</span><span class="sxs-lookup"><span data-stu-id="8785c-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="8785c-157">この動作は、 :::no-loc(Razor)::: エンコーダーを使用して文字列を出力するため、taghelper と HtmlHelper のレンダリングにも影響します。</span><span class="sxs-lookup"><span data-stu-id="8785c-157">This behavior also affects :::no-loc(Razor)::: TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="8785c-158">この理由は、未知のブラウザーバグや今後のブラウザーバグから保護するためのものです (以前のブラウザーのバグは、英語以外の文字の処理に基づいて解析されました)。</span><span class="sxs-lookup"><span data-stu-id="8785c-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="8785c-159">Web サイトで、中国語、キリル語などのラテン文字以外の文字を頻繁に使用する場合、これは必要な動作ではないと思います。</span><span class="sxs-lookup"><span data-stu-id="8785c-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="8785c-160">エンコーダーセーフリストをカスタマイズして、起動時にアプリケーションに適した Unicode 範囲をに含めることができ `ConfigureServices()` ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="8785c-161">たとえば、既定の構成を使用する場合は、 :::no-loc(Razor)::: 次のように htmlhelper を使用します。</span><span class="sxs-lookup"><span data-stu-id="8785c-161">For example, using the default configuration you might use a :::no-loc(Razor)::: HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="8785c-162">Web ページのソースを表示すると、次のように、中国語のテキストがエンコードされた状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="8785c-163">エンコーダーによって安全として扱われる文字を拡大するには、のメソッドに次の行を挿入し `ConfigureServices()` `startup.cs` ます。</span><span class="sxs-lookup"><span data-stu-id="8785c-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="8785c-164">この例では、セーフリストを拡大して、CjkUnifiedIdeographs という Unicode 範囲を追加しています。</span><span class="sxs-lookup"><span data-stu-id="8785c-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="8785c-165">レンダリングされた出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8785c-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="8785c-166">セーフリストの範囲は、言語ではなく、Unicode コードグラフとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="8785c-167">[Unicode 規格](https://unicode.org/)には、文字を含むグラフを検索するために使用できる[コード表](https://www.unicode.org/charts/index.html)の一覧があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="8785c-168">各エンコーダー、Html、JavaScript、および Url は、個別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8785c-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="8785c-169">セーフリストのカスタマイズは、DI によって供給されるエンコーダーにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="8785c-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="8785c-170">既定のを使用してエンコーダーに直接アクセスした場合は `System.Text.Encodings.Web.*Encoder.Default` 、基本的なラテン only セーフセーフセーフポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8785c-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="8785c-171">エンコードを行う場所</span><span class="sxs-lookup"><span data-stu-id="8785c-171">Where should encoding take place?</span></span>

<span data-ttu-id="8785c-172">一般に、エンコードは出力時に行われ、エンコードされた値をデータベースに格納しないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8785c-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="8785c-173">出力の時点でエンコードを使用すると、たとえば HTML からクエリ文字列の値まで、データの使用を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="8785c-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="8785c-174">また、検索の前に値をエンコードしなくてもデータを簡単に検索でき、エンコーダーに加えられた変更やバグ修正を利用できます。</span><span class="sxs-lookup"><span data-stu-id="8785c-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="8785c-175">XSS 防止手法としての検証</span><span class="sxs-lookup"><span data-stu-id="8785c-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="8785c-176">検証は、XSS 攻撃を制限するうえで役に立つツールです。</span><span class="sxs-lookup"><span data-stu-id="8785c-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="8785c-177">たとえば、文字0-9 のみを含む数値文字列は、XSS 攻撃をトリガーしません。</span><span class="sxs-lookup"><span data-stu-id="8785c-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="8785c-178">ユーザー入力で HTML を受け入れると、検証がより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="8785c-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="8785c-179">HTML 入力の解析は、不可能な場合には困難です。</span><span class="sxs-lookup"><span data-stu-id="8785c-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="8785c-180">Markdown は、埋め込まれた HTML を取り除くパーサーと組み合わせることで、リッチな入力を受け入れるためのより安全な選択肢となります。</span><span class="sxs-lookup"><span data-stu-id="8785c-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="8785c-181">検証だけに依存しない。</span><span class="sxs-lookup"><span data-stu-id="8785c-181">Never rely on validation alone.</span></span> <span data-ttu-id="8785c-182">検証やサニタイズが実行されているかどうかにかかわらず、常に信頼されていない入力を出力の前にエンコードします。</span><span class="sxs-lookup"><span data-stu-id="8785c-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
