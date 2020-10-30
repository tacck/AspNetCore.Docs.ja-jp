---
title: ASP.NET Core のリンク タグ ヘルパー
author: rick-anderson
ms.author: riande
description: ASP.NET Core リンク タグ ヘルパーの属性と、HTML リンク タグの動作拡張時の各属性の役割を示します。
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059118"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="6b019-103">ASP.NET Core のリンク タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6b019-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="6b019-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b019-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6b019-105">[リンク タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)によって、プライマリまたはフォール バック CSS ファイルへのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b019-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="6b019-106">通常、プライマリ CSS ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。</span><span class="sxs-lookup"><span data-stu-id="6b019-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="6b019-107">リンク タグ ヘルパーを使用すると、CSS ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。</span><span class="sxs-lookup"><span data-stu-id="6b019-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="6b019-108">リンク タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。</span><span class="sxs-lookup"><span data-stu-id="6b019-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="6b019-109">次の :::no-loc(Razor)::: マークアップは、 `head` ASP.NET Core web アプリテンプレートを使用して作成されたレイアウトファイルの要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="6b019-109">The following :::no-loc(Razor)::: markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="6b019-110">次に示すのは、前のコードからレンダリングされた HTML です (非開発環境)。</span><span class="sxs-lookup"><span data-stu-id="6b019-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="6b019-111">前のコードでは、リンク タグ ヘルパーによって `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 要素が生成され、要求された *bootstrap.min.css* ファイルが CDN 上にあることを確認するために使用される次の JavaScript も生成されました。</span><span class="sxs-lookup"><span data-stu-id="6b019-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="6b019-112">このケースでは、CSS ファイルは存在しており、タグ ヘルパーによって CDN CSS ファイルを含む `<link />` 要素が生成されました。</span><span class="sxs-lookup"><span data-stu-id="6b019-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="6b019-113">一般的に使用されるリンク タグ ヘルパー属性</span><span class="sxs-lookup"><span data-stu-id="6b019-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="6b019-114">リンク タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[リンク タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b019-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="6b019-115">href</span><span class="sxs-lookup"><span data-stu-id="6b019-115">href</span></span>

<span data-ttu-id="6b019-116">リンクされたリソースの優先アドレス。</span><span class="sxs-lookup"><span data-stu-id="6b019-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="6b019-117">このアドレスは、生成された HTML に常に渡されます。</span><span class="sxs-lookup"><span data-stu-id="6b019-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="6b019-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="6b019-118">asp-fallback-href</span></span>

<span data-ttu-id="6b019-119">プライマリ URL が失敗した場合にフォールバックする CSS スタイルシートの URL。</span><span class="sxs-lookup"><span data-stu-id="6b019-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="6b019-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="6b019-120">asp-fallback-test-class</span></span>

<span data-ttu-id="6b019-121">フォールバック テストで使用するためにスタイルシートに定義されているクラス名。</span><span class="sxs-lookup"><span data-stu-id="6b019-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="6b019-122">詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b019-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="6b019-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="6b019-123">asp-fallback-test-property</span></span>

<span data-ttu-id="6b019-124">フォールバック テストで使用する CSS プロパティ名。</span><span class="sxs-lookup"><span data-stu-id="6b019-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="6b019-125">詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b019-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="6b019-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="6b019-126">asp-fallback-test-value</span></span>

<span data-ttu-id="6b019-127">フォールバック テストで使用する CSS プロパティ値。</span><span class="sxs-lookup"><span data-stu-id="6b019-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="6b019-128">詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b019-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b019-129">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6b019-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
