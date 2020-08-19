---
title: ASP.NET Core のアンカー タグ ヘルパー
author: pkellner
description: ASP.NET Core アンカー タグ ヘルパーの属性と、HTML アンカー タグの動作拡張時の各属性の役割を示します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: fcb58e2e0bfc6598edeb8c7f79986c3faf84e316
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633930"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="ac12e-103">ASP.NET Core のアンカー タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="ac12e-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="ac12e-104">作成者: [Peter Kellner](https://peterkellner.net)、[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="ac12e-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="ac12e-105">[アンカー タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper)は、新しい属性を追加することで標準の HTML アンカー (`<a ... ></a>`) タグを拡張します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="ac12e-106">規則では、属性名の前に `asp-` が付きます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="ac12e-107">レンダリングされたアンカー要素の `href` 属性値は、`asp-` 属性の値によって決まります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="ac12e-108">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ac12e-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="ac12e-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ac12e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ac12e-110">*SpeakerController* は、このドキュメント全体にわたってサンプルとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="ac12e-111">アンカー タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="ac12e-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="ac12e-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="ac12e-112">asp-controller</span></span>

<span data-ttu-id="ac12e-113">[asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) 属性は、URL の生成に使用するコント ローラーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="ac12e-114">次のマークアップでは、すべてのスピーカーが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="ac12e-115">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="ac12e-116">`asp-controller` 属性が指定されていて、`asp-action` が指定されていない場合は、既定値 `asp-action` が現在実行中のビューに関連付けられたコント ローラー アクションです。</span><span class="sxs-lookup"><span data-stu-id="ac12e-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="ac12e-117">`asp-action` が前のマークアップから省略されていて、アンカー タグ ヘルパーが *HomeController* の *Index* ビュー (*/Home*) で使用されている場合、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="ac12e-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="ac12e-118">asp-action</span></span>

<span data-ttu-id="ac12e-119">[asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) 属性値は、生成された `href` 属性に含まれるコントローラー アクション名を表します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="ac12e-120">次のマークアップは、生成された `href` 属性値をスピーカー評価ページに設定します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="ac12e-121">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="ac12e-122">`asp-controller` 属性が指定されていない場合、現在のビューを実行しているビューを呼び出す既定のコントローラーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="ac12e-123">属性値 `asp-action` が `Index` で、URL にアクションが何も追加されていない場合、既定の `Index` の操作が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="ac12e-124">指定された (または既定の) 操作が、`asp-controller` で参照されるコントローラーに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="ac12e-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="ac12e-125">asp-route-{value}</span></span>

<span data-ttu-id="ac12e-126">[asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性は、ワイルドカード ルート プレフィックスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="ac12e-127">`{value}` プレースホルダーに入っている値はすべて、潜在的なルートのパラメーターとして解釈されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="ac12e-128">既定のルートが見つからない場合は、このルート プレフィックスが生成された `href` に要求パラメーターおよび値として追加されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="ac12e-129">見つかった場合は、そのルートがルート テンプレートで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="ac12e-130">次のコントローラー アクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="ac12e-131">既定のルート テンプレートは *Startup.Configure* に定義されています。</span><span class="sxs-lookup"><span data-stu-id="ac12e-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="ac12e-132">MVC ビューは、次のように、アクションによって提供されるモデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="ac12e-133">既定のルートの `{id?}` プレースホルダーが一致しました。</span><span class="sxs-lookup"><span data-stu-id="ac12e-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="ac12e-134">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="ac12e-135">次の MVC ビューのように、ルート プレフィックスが一致するルーティング テンプレートに含まれないものとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="ac12e-136">`speakerid` が一致するルートに見つからなかったため、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="ac12e-137">`asp-controller` または `asp-action` のどちらかが指定されていない場合は、`asp-route` 属性の場合と同じ既定の処理に従います。</span><span class="sxs-lookup"><span data-stu-id="ac12e-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="ac12e-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="ac12e-138">asp-route</span></span>

<span data-ttu-id="ac12e-139">[asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) 属性は、名前付きのルートに直接 URL リンクを作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="ac12e-140">[ルーティング属性](xref:mvc/controllers/routing#attribute-routing)を使用すると、`SpeakerController` に示されているようにルートに名前を付け、その `Evaluations` アクションで使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="ac12e-141">次のマークアップでは、`asp-route` 属性が名前付きのルートを参照します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="ac12e-142">アンカー タグ ヘルパーは URL */Speaker/Evaluations* を使用してそのコントローラー アクションに直接ルートを生成します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="ac12e-143">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="ac12e-144">`asp-controller` または `asp-action` が `asp-route` の他に指定されていると、想定と異なるルートが生成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="ac12e-145">ルートの競合を防ぐには、`asp-route` を属性 `asp-controller` および `asp-action` とともに使用してはいけません。</span><span class="sxs-lookup"><span data-stu-id="ac12e-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="ac12e-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="ac12e-146">asp-all-route-data</span></span>

<span data-ttu-id="ac12e-147">[asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性は、キー/値ペアのディクショナリの作成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="ac12e-148">キーはパラメーターの名前で、値はパラメーターの値です。</span><span class="sxs-lookup"><span data-stu-id="ac12e-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="ac12e-149">次の例では、ディクショナリが初期化され、ビューに渡され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="ac12e-150">データがモデルによって渡される場合もあります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="ac12e-151">上のコードを実行すると、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="ac12e-152">`asp-all-route-data` ディクショナリは、オーバー ロードされた `Evaluations` アクションの要件を満たすクエリ文字列を生成するためにフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="ac12e-153">ディクショナリ内のいずれかのキーがルート パラメーターと一致する場合は、その値がルートで適宜置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="ac12e-154">その他の一致しない値は要求パラメーターとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="ac12e-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="ac12e-155">asp-fragment</span></span>

<span data-ttu-id="ac12e-156">[asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) 属性では URL に追加される URL フラグメントが定義されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="ac12e-157">アンカー タグ ヘルパーにより、ハッシュ文字 (#) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="ac12e-158">次のマークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="ac12e-159">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="ac12e-160">ハッシュ タグはクライアント側アプリを構築するときに便利です。</span><span class="sxs-lookup"><span data-stu-id="ac12e-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="ac12e-161">たとえば、JavaScript でのマーク付けや検索を簡単にするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="ac12e-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="ac12e-162">asp-area</span></span>

<span data-ttu-id="ac12e-163">[asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) 属性は、適切なルートの設定に使用する領域名を設定します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="ac12e-164">`asp-area` 属性によってどのようにルートの再マップが行われるかの例を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-no-locrazor-pages"></a><span data-ttu-id="ac12e-165">使用 ( Razor ページで)</span><span class="sxs-lookup"><span data-stu-id="ac12e-165">Usage in Razor Pages</span></span>

<span data-ttu-id="ac12e-166">Razor ページ領域は ASP.NET Core 2.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ac12e-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="ac12e-167">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="ac12e-168">**{プロジェクト名}**</span><span class="sxs-lookup"><span data-stu-id="ac12e-168">**{Project name}**</span></span>
  * <span data-ttu-id="ac12e-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="ac12e-169">**wwwroot**</span></span>
  * <span data-ttu-id="ac12e-170">**領域**</span><span class="sxs-lookup"><span data-stu-id="ac12e-170">**Areas**</span></span>
    * <span data-ttu-id="ac12e-171">**セッション**</span><span class="sxs-lookup"><span data-stu-id="ac12e-171">**Sessions**</span></span>
      * <span data-ttu-id="ac12e-172">**ページ**</span><span class="sxs-lookup"><span data-stu-id="ac12e-172">**Pages**</span></span>
        * <span data-ttu-id="ac12e-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ac12e-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="ac12e-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ac12e-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="ac12e-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ac12e-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="ac12e-176">**ページ**</span><span class="sxs-lookup"><span data-stu-id="ac12e-176">**Pages**</span></span>

<span data-ttu-id="ac12e-177">[ *セッション* 領域の *インデックス*] ページを参照するマークアップ Razor は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ac12e-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="ac12e-178">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="ac12e-179">Pages アプリの領域をサポートするには Razor 、で次のいずれかの操作を行い `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="ac12e-180">[互換性バージョン](xref:mvc/compatibility-version)に 2.1 以降を設定します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="ac12e-181">次のように、[ [ Razor allowareas]](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*)プロパティをに設定します。 `true`</span><span class="sxs-lookup"><span data-stu-id="ac12e-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="ac12e-182">MVC の使用法</span><span class="sxs-lookup"><span data-stu-id="ac12e-182">Usage in MVC</span></span>

<span data-ttu-id="ac12e-183">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="ac12e-184">**{プロジェクト名}**</span><span class="sxs-lookup"><span data-stu-id="ac12e-184">**{Project name}**</span></span>
  * <span data-ttu-id="ac12e-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="ac12e-185">**wwwroot**</span></span>
  * <span data-ttu-id="ac12e-186">**領域**</span><span class="sxs-lookup"><span data-stu-id="ac12e-186">**Areas**</span></span>
    * <span data-ttu-id="ac12e-187">**ブログ**</span><span class="sxs-lookup"><span data-stu-id="ac12e-187">**Blogs**</span></span>
      * <span data-ttu-id="ac12e-188">**コントローラー**</span><span class="sxs-lookup"><span data-stu-id="ac12e-188">**Controllers**</span></span>
        * <span data-ttu-id="ac12e-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="ac12e-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="ac12e-190">**ビュー**</span><span class="sxs-lookup"><span data-stu-id="ac12e-190">**Views**</span></span>
        * <span data-ttu-id="ac12e-191">**Home**</span><span class="sxs-lookup"><span data-stu-id="ac12e-191">**Home**</span></span>
          * <span data-ttu-id="ac12e-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ac12e-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="ac12e-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ac12e-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="ac12e-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ac12e-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="ac12e-195">**コントローラー**</span><span class="sxs-lookup"><span data-stu-id="ac12e-195">**Controllers**</span></span>

<span data-ttu-id="ac12e-196">`asp-area` を [ブログ] に設定すると、このアンカー タグの関連付けられているコントローラーとビューのルートに、ディレクトリ *Areas/Blogs* のプレフィックスが付けられます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="ac12e-197">*AboutBlog* ビューを参照するマークアップは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ac12e-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="ac12e-198">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="ac12e-199">MVC アプリで領域をサポートするには、ルート テンプレートに領域への参照 (存在する場合) が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="ac12e-200">そのテンプレートは、*Startup.Configure* の `routes.MapRoute` メソッド呼び出しの 2 番目のパラメーターで表されます</span><span class="sxs-lookup"><span data-stu-id="ac12e-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="ac12e-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="ac12e-201">asp-protocol</span></span>

<span data-ttu-id="ac12e-202">[asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) 属性は URL に (`https` などの) プロトコルを指定するためにあります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="ac12e-203">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="ac12e-204">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="ac12e-205">例ではホスト名が localhost です。</span><span class="sxs-lookup"><span data-stu-id="ac12e-205">The host name in the example is localhost.</span></span> <span data-ttu-id="ac12e-206">アンカー タグ ヘルパーは、URL を生成するときに Web サイトのパブリック ドメインを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="ac12e-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="ac12e-207">asp-host</span></span>

<span data-ttu-id="ac12e-208">[asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) 属性は URL のホスト名を指定するためにあります。</span><span class="sxs-lookup"><span data-stu-id="ac12e-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="ac12e-209">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="ac12e-210">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="ac12e-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="ac12e-211">asp-page</span></span>

<span data-ttu-id="ac12e-212">[Asp ページ](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*)属性は、ページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="ac12e-213">アンカー タグの `href` 属性の値を特定のページに設定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="ac12e-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="ac12e-214">ページ名の前にスラッシュ "/" を付けると URL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="ac12e-215">次の例では、出席者ページを参照してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="ac12e-216">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="ac12e-217">`asp-page` 属性は `asp-route`、`asp-controller`、`asp-action` の各属性と相互に排他的です。</span><span class="sxs-lookup"><span data-stu-id="ac12e-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="ac12e-218">ただし、`asp-page` は、次のマークアップのサンプルで示されているように、`asp-route-{value}` とともに使用してルーティングを制御できます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="ac12e-219">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="ac12e-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="ac12e-220">asp-page-handler</span></span>

<span data-ttu-id="ac12e-221">[Asp ページハンドラー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*)属性は、ページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="ac12e-222">特定のページ ハンドラーへのリンクが目的です。</span><span class="sxs-lookup"><span data-stu-id="ac12e-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="ac12e-223">次のページ ハンドラーがあるとします。</span><span class="sxs-lookup"><span data-stu-id="ac12e-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="ac12e-224">ページ モデルの関連するマークアップが `OnGetProfile` ページ ハンドラーにリンクしています。</span><span class="sxs-lookup"><span data-stu-id="ac12e-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="ac12e-225">ページ ハンドラーのメソッド名の `On<Verb>` プレフィックスは `asp-page-handler` 属性値では省略されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ac12e-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="ac12e-226">メソッドが非同期の場合は、`Async` サフィックスも省略されます。</span><span class="sxs-lookup"><span data-stu-id="ac12e-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="ac12e-227">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="ac12e-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="ac12e-228">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ac12e-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
