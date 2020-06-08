---
title: 'title:ASP.NET Core で Portable Object のローカライズを構成する author: sebastienros description:この記事では、Portable Object (PO) ファイルについて紹介します。また、Orchard Core フレームワークを使用する ASP.NET Core アプリケーションで PO ファイルを使用する手順について説明します。'
author: sebastienros
description: 'ms.author: scaddie ms.date:09/26/2017 no-loc:'
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355085"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="486be-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="486be-103">'Blazor'</span></span>

<span data-ttu-id="486be-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="486be-104">'Identity'</span></span>

<span data-ttu-id="486be-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="486be-105">'Let's Encrypt'</span></span>

<span data-ttu-id="486be-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="486be-106">'Razor'</span></span> <span data-ttu-id="486be-107">'SignalR' uid: fundamentals/portable-object-localization</span><span class="sxs-lookup"><span data-stu-id="486be-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

<span data-ttu-id="486be-108">ASP.NET Core で Portable Object のローカライズを構成する</span><span class="sxs-lookup"><span data-stu-id="486be-108">Configure portable object localization in ASP.NET Core</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="486be-109">作成者: [Sébastien Ros](https://github.com/sebastienros)、[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="486be-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="486be-110">この記事では、[Orchard Core](https://github.com/OrchardCMS/OrchardCore) フレームワークを使用した ASP.NET Core アプリケーションで Portable Object (PO) ファイルを使用する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="486be-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span> <span data-ttu-id="486be-111">**注:** Orchard Core は Microsoft 製品ではありません。</span><span class="sxs-lookup"><span data-stu-id="486be-111">**Note:** Orchard Core isn't a Microsoft product.</span></span>
- <span data-ttu-id="486be-112">したがって、Microsoft はこの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="486be-112">Consequently, Microsoft provides no support for this feature.</span></span>
- <span data-ttu-id="486be-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="486be-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="486be-114">PO ファイルとは</span><span class="sxs-lookup"><span data-stu-id="486be-114">What is a PO file?</span></span>
- <span data-ttu-id="486be-115">PO ファイルは、特定の言語の翻訳済み文字列を含むテキスト ファイルとして配布されます。</span><span class="sxs-lookup"><span data-stu-id="486be-115">PO files are distributed as text files containing the translated strings for a given language.</span></span>

### <a name="example"></a><span data-ttu-id="486be-116">*.resx* ファイルの代わりに PO ファイルを使用すると、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="486be-116">Some advantages of using PO files instead *.resx* files include:</span></span>

<span data-ttu-id="486be-117">PO ファイルは複数形化をサポートしています。 *.resx* ファイルは複数形化をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="486be-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>

<span data-ttu-id="486be-118">PO ファイルは *.resx* ファイルのようにコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="486be-118">PO files aren't compiled like *.resx* files.</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="486be-119">そのため、特殊なツールやビルドの手順は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="486be-119">As such, specialized tooling and build steps aren't required.</span></span>

- <span data-ttu-id="486be-120">PO ファイルは、オンラインの共同編集ツールでの利用に適しています。</span><span class="sxs-lookup"><span data-stu-id="486be-120">PO files work well with collaborative online editing tools.</span></span> <span data-ttu-id="486be-121">例</span><span class="sxs-lookup"><span data-stu-id="486be-121">Example</span></span>
- <span data-ttu-id="486be-122">フランス語の 2 つの文字列 (一方は複数形も含まれています) の翻訳が記載されたサンプル PO ファイルを次に示します。</span><span class="sxs-lookup"><span data-stu-id="486be-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>
- <span data-ttu-id="486be-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="486be-123">*fr.po*</span></span>

<span data-ttu-id="486be-124">この例では、次の構文を使用します。</span><span class="sxs-lookup"><span data-stu-id="486be-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="486be-125">`#:`:翻訳される文字列のコンテキストを示すコメント。</span><span class="sxs-lookup"><span data-stu-id="486be-125">`#:`: A comment indicating the context of the string to be translated.</span></span>
- <span data-ttu-id="486be-126">同じ文字列でも、使用される場所によって翻訳が変わることがあります。</span><span class="sxs-lookup"><span data-stu-id="486be-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="486be-127">`msgid`:翻訳前の文字列。</span><span class="sxs-lookup"><span data-stu-id="486be-127">`msgid`: The untranslated string.</span></span>

<span data-ttu-id="486be-128">`msgstr`:翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="486be-128">`msgstr`: The translated string.</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="486be-129">複数形化をサポートする場合は、その他のエントリも定義できます。</span><span class="sxs-lookup"><span data-stu-id="486be-129">In the case of pluralization support, more entries can be defined.</span></span>

<span data-ttu-id="486be-130">`msgid_plural`:翻訳前の文字列の複数形。</span><span class="sxs-lookup"><span data-stu-id="486be-130">`msgid_plural`: The untranslated plural string.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="486be-131">`msgstr[0]`:0 の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="486be-131">`msgstr[0]`: The translated string for the case 0.</span></span>

<span data-ttu-id="486be-132">`msgstr[N]`:N の場合の翻訳後の文字列。</span><span class="sxs-lookup"><span data-stu-id="486be-132">`msgstr[N]`: The translated string for the case N.</span></span> <span data-ttu-id="486be-133">PO ファイルの仕様については、[こちら](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="486be-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

<span data-ttu-id="486be-134">ASP.NET Core で PO ファイルのサポートを構成する</span><span class="sxs-lookup"><span data-stu-id="486be-134">Configuring PO file support in ASP.NET Core</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="486be-135">この例は、Visual Studio 2017 プロジェクト テンプレートから生成された ASP.NET Core MVC アプリケーションに基づいています。</span><span class="sxs-lookup"><span data-stu-id="486be-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

<span data-ttu-id="486be-136">パッケージの参照</span><span class="sxs-lookup"><span data-stu-id="486be-136">Referencing the package</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="486be-137">`OrchardCore.Localization.Core` NuGet パッケージの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="486be-138">[MyGet](https://www.myget.org/) のパッケージ ソース https://www.myget.org/F/orchardcore-preview/api/v3/index.json で入手できます。</span><span class="sxs-lookup"><span data-stu-id="486be-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span> <span data-ttu-id="486be-139">*.csproj* ファイルに次のような行が追加されました (バージョン番号は異なる可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="486be-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="486be-140">サービスの登録</span><span class="sxs-lookup"><span data-stu-id="486be-140">Registering the service</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="486be-141">*Startup.cs* の `ConfigureServices` メソッドに必要なサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="486be-142">*Startup.cs* の `Configure` メソッドに必要なミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span> <span data-ttu-id="486be-143">選択した Razor ビューに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-143">Add the following code to your Razor view of choice.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="486be-144">この例では *About.cshtml* を使用します。</span><span class="sxs-lookup"><span data-stu-id="486be-144">*About.cshtml* is used in this example.</span></span> <span data-ttu-id="486be-145">"Hello world!" というテキストを翻訳するために、`IViewLocalizer` インスタンスが挿入され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="486be-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span> <span data-ttu-id="486be-146">PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="486be-146">Creating a PO file</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="486be-147">アプリケーションのルート フォルダーに *\<culture code>.po* というファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="486be-147">Create a file named *\<culture code>.po* in your application root folder.</span></span>

<span data-ttu-id="486be-148">この例では、フランス語が使用されているため、ファイル名は *fr.po* です。</span><span class="sxs-lookup"><span data-stu-id="486be-148">In this example, the file name is *fr.po* because the French language is used:</span></span> <span data-ttu-id="486be-149">このファイルには、翻訳する文字列とフランス語で翻訳された文字列の両方が格納されています。</span><span class="sxs-lookup"><span data-stu-id="486be-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="486be-150">必要に応じて、翻訳は元の親カルチャに戻されます。</span><span class="sxs-lookup"><span data-stu-id="486be-150">Translations revert to their parent culture, if necessary.</span></span>

<span data-ttu-id="486be-151">この例では、要求されるカルチャが `fr-FR` または `fr-CA` の場合、*fr.po* ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="486be-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span> <span data-ttu-id="486be-152">アプリケーションのテスト</span><span class="sxs-lookup"><span data-stu-id="486be-152">Testing the application</span></span> <span data-ttu-id="486be-153">アプリケーションを実行し、URL `/Home/About` に移動します。</span><span class="sxs-lookup"><span data-stu-id="486be-153">Run your application, and navigate to the URL `/Home/About`.</span></span>

## <a name="pluralization"></a><span data-ttu-id="486be-154">テキスト **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="486be-154">The text **Hello world!**</span></span>

<span data-ttu-id="486be-155">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="486be-155">is displayed.</span></span> <span data-ttu-id="486be-156">URL `/Home/About?culture=fr-FR` に移動します。</span><span class="sxs-lookup"><span data-stu-id="486be-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span>

<span data-ttu-id="486be-157">テキスト **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="486be-157">The text **Bonjour le monde!**</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="486be-158">が表示されます。</span><span class="sxs-lookup"><span data-stu-id="486be-158">is displayed.</span></span>

<span data-ttu-id="486be-159">複数形化</span><span class="sxs-lookup"><span data-stu-id="486be-159">Pluralization</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="486be-160">PO ファイルは複数形化フォームをサポートしています。これは、カーディナリティに基づいて同じ文字列の翻訳を変える必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="486be-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="486be-161">各言語で、カーディナリティに基づいて使用する文字列を選択するためのカスタム ルールが定義されているため、このタスクは複雑になります。</span><span class="sxs-lookup"><span data-stu-id="486be-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="486be-162">Orchard Localization パッケージには、このような異なる複数形を自動的に呼び出す API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="486be-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span> <span data-ttu-id="486be-163">複数形化 PO ファイルの作成</span><span class="sxs-lookup"><span data-stu-id="486be-163">Creating pluralization PO files</span></span> <span data-ttu-id="486be-164">前述の *fr.po* ファイルに次の内容を追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

<span data-ttu-id="486be-165">この例の各エントリが表す内容の説明については、「[PO ファイルとは](#what-is-a-po-file)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="486be-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span> <span data-ttu-id="486be-166">異なる複数形化フォームを使用する言語の追加</span><span class="sxs-lookup"><span data-stu-id="486be-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="486be-167">前の例では、英語とフランス語の文字列が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="486be-167">English and French strings were used in the previous example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="486be-168">英語とフランス語の複数形化フォームは 2 つのみであり、同じフォーム ルールを共有しています。つまり、1 のカーディナリティは最初の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="486be-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="486be-169">その他のカーディナリティは 2 番目の複数形にマップされます。</span><span class="sxs-lookup"><span data-stu-id="486be-169">Any other cardinality is mapped to the second plural form.</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="486be-170">すべての言語が同じルールを共有するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="486be-170">Not all languages share the same rules.</span></span> <span data-ttu-id="486be-171">たとえば、チェコ語の場合は 3 つの複数形があります。</span><span class="sxs-lookup"><span data-stu-id="486be-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="486be-172">`cs.po` ファイルを次のように作成し、複数形化に 3 つの異なる翻訳がどのように必要かを記述します。</span><span class="sxs-lookup"><span data-stu-id="486be-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

<span data-ttu-id="486be-173">チェコ語のローカライズを引き受ける場合は、`ConfigureServices` メソッドでサポートされるカルチャのリストに `"cs"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="486be-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="486be-174">複数の基数についてローカライズされた複数形の文字列をレンダリングするように、*Views/Home/About.cshtml* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="486be-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="486be-175">**注:** 実際のシナリオでは、変数を使用してカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="486be-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="486be-176">ここでは、非常に特殊なケースを表すために、3 つの異なる値を使用して同じコードを繰り返しています。</span><span class="sxs-lookup"><span data-stu-id="486be-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span> <span data-ttu-id="486be-177">カルチャを切り替えると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="486be-177">Upon switching cultures, you see the following:</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="486be-178">`/Home/About`の場合:</span><span class="sxs-lookup"><span data-stu-id="486be-178">For `/Home/About`:</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="486be-179">`/Home/About?culture=fr`の場合:</span><span class="sxs-lookup"><span data-stu-id="486be-179">For `/Home/About?culture=fr`:</span></span>

<span data-ttu-id="486be-180">`/Home/About?culture=cs`の場合:</span><span class="sxs-lookup"><span data-stu-id="486be-180">For `/Home/About?culture=cs`:</span></span> <span data-ttu-id="486be-181">チェコ語のカルチャの場合、3 つの翻訳が異なることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="486be-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="486be-182">フランス語と英語のカルチャは、翻訳後の文字列のうち、後半 2 つは同じ構造を共有しています。</span><span class="sxs-lookup"><span data-stu-id="486be-182">The French and English cultures share the same construction for the two last translated strings.</span></span> <span data-ttu-id="486be-183">高度なタスク</span><span class="sxs-lookup"><span data-stu-id="486be-183">Advanced tasks</span></span>

<span data-ttu-id="486be-184">文字列のコンテキスト化</span><span class="sxs-lookup"><span data-stu-id="486be-184">Contextualizing strings</span></span> <span data-ttu-id="486be-185">多くの場合、アプリケーションには複数の場所で翻訳される文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="486be-185">Applications often contain the strings to be translated in several places.</span></span>

<span data-ttu-id="486be-186">同じ文字列でも、アプリ内の場所 (Razor ビューやクラス ファイル) によっては翻訳が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="486be-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="486be-187">PO ファイルは、表現されている文字列の分類に使用できるファイル コンテキストの概念をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="486be-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="486be-188">ファイル コンテキストを使用すると、ファイル コンテキスト (またはファイル コンテキストの欠如) に応じて文字列の翻訳を変えることができます。</span><span class="sxs-lookup"><span data-stu-id="486be-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span> <span data-ttu-id="486be-189">PO ローカライズ サービスは、文字列を翻訳するときに使用される完全クラスまたはビューの名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="486be-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span>

<span data-ttu-id="486be-190">これは、`msgctxt` エントリに値を設定することによって行います。</span><span class="sxs-lookup"><span data-stu-id="486be-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span> <span data-ttu-id="486be-191">前述の *fr.po* の例に少し追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="486be-191">Consider a minor addition to the previous *fr.po* example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="486be-192">*Views/Home/About.cshtml* にある Razor ビューは、予約された `msgctxt` エントリの値を設定することでファイル コンテキストとして定義できます。</span><span class="sxs-lookup"><span data-stu-id="486be-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

<span data-ttu-id="486be-193">`msgctxt` をこのように設定すると、`/Home/About?culture=fr-FR` に移動するときにテキストの翻訳が行われます。</span><span class="sxs-lookup"><span data-stu-id="486be-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="486be-194">`/Home/Contact?culture=fr-FR` に移動するときに翻訳は行われません。</span><span class="sxs-lookup"><span data-stu-id="486be-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="486be-195">特定のエントリが特定のファイル コンテキストと一致しない場合、Orchard Core のフォールバック メカニズムによって、コンテキストなしで適切な PO ファイルが検索されます。</span><span class="sxs-lookup"><span data-stu-id="486be-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span>

<span data-ttu-id="486be-196">*Views/Home/Contact.cshtml* に特定のファイル コンテキストが定義されていない場合、`/Home/Contact?culture=fr-FR` に移動すると、次のような PO ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="486be-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span> <span data-ttu-id="486be-197">PO ファイルの場所の変更</span><span class="sxs-lookup"><span data-stu-id="486be-197">Changing the location of PO files</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="486be-198">PO ファイルの既定の場所は、`ConfigureServices` で変更できます。</span><span class="sxs-lookup"><span data-stu-id="486be-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

<span data-ttu-id="486be-199">この例では、PO ファイルは *Localization* フォルダーから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="486be-199">In this example, the PO files are loaded from the *Localization* folder.</span></span> <span data-ttu-id="486be-200">ローカライズ ファイルを検索するためのカスタム ロジックの実装</span><span class="sxs-lookup"><span data-stu-id="486be-200">Implementing a custom logic for finding localization files</span></span> <span data-ttu-id="486be-201">PO ファイルを特定するためにより複雑なロジックが必要な場合は、`OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` インターフェイスを実装してサービスとして登録することができます。</span><span class="sxs-lookup"><span data-stu-id="486be-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span>

<span data-ttu-id="486be-202">これは、PO ファイルをさまざまな場所に格納できる場合や、複数のフォルダーがある 1 階層内でファイルを検出する必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="486be-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>
