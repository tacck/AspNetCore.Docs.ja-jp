---
title: ASP.NET Core のアンカー タグ ヘルパー
author: pkellner
description: ASP.NET Core アンカー タグ ヘルパーの属性と、HTML アンカー タグの動作拡張時の各属性の役割を示します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
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
ms.openlocfilehash: 25f1716b5c80492edb72bc9555954c9c93c02a49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018767"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="62833-103">ASP.NET Core のアンカー タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="62833-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="62833-104">作成者: [Peter Kellner](https://peterkellner.net)、[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="62833-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="62833-105">[アンカー タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper)は、新しい属性を追加することで標準の HTML アンカー (`<a ... ></a>`) タグを拡張します。</span><span class="sxs-lookup"><span data-stu-id="62833-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="62833-106">規則では、属性名の前に `asp-` が付きます。</span><span class="sxs-lookup"><span data-stu-id="62833-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="62833-107">レンダリングされたアンカー要素の `href` 属性値は、`asp-` 属性の値によって決まります。</span><span class="sxs-lookup"><span data-stu-id="62833-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="62833-108">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="62833-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="62833-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="62833-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="62833-110">*SpeakerController* は、このドキュメント全体にわたってサンプルとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="62833-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="62833-111">アンカー タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="62833-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="62833-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="62833-112">asp-controller</span></span>

<span data-ttu-id="62833-113">[asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) 属性は、URL の生成に使用するコント ローラーを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="62833-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="62833-114">次のマークアップでは、すべてのスピーカーが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="62833-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="62833-115">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="62833-116">`asp-controller` 属性が指定されていて、`asp-action` が指定されていない場合は、既定値 `asp-action` が現在実行中のビューに関連付けられたコント ローラー アクションです。</span><span class="sxs-lookup"><span data-stu-id="62833-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="62833-117">`asp-action` が前のマークアップから省略されていて、アンカー タグ ヘルパーが *HomeController* の *Index* ビュー (*/Home*) で使用されている場合、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="62833-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="62833-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="62833-118">asp-action</span></span>

<span data-ttu-id="62833-119">[asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) 属性値は、生成された `href` 属性に含まれるコントローラー アクション名を表します。</span><span class="sxs-lookup"><span data-stu-id="62833-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="62833-120">次のマークアップは、生成された `href` 属性値をスピーカー評価ページに設定します。</span><span class="sxs-lookup"><span data-stu-id="62833-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="62833-121">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="62833-122">`asp-controller` 属性が指定されていない場合、現在のビューを実行しているビューを呼び出す既定のコントローラーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="62833-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="62833-123">属性値 `asp-action` が `Index` で、URL にアクションが何も追加されていない場合、既定の `Index` の操作が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="62833-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="62833-124">指定された (または既定の) 操作が、`asp-controller` で参照されるコントローラーに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="62833-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="62833-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="62833-125">asp-route-{value}</span></span>

<span data-ttu-id="62833-126">[asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性は、ワイルドカード ルート プレフィックスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="62833-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="62833-127">`{value}` プレースホルダーに入っている値はすべて、潜在的なルートのパラメーターとして解釈されます。</span><span class="sxs-lookup"><span data-stu-id="62833-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="62833-128">既定のルートが見つからない場合は、このルート プレフィックスが生成された `href` に要求パラメーターおよび値として追加されます。</span><span class="sxs-lookup"><span data-stu-id="62833-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="62833-129">見つかった場合は、そのルートがルート テンプレートで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="62833-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="62833-130">次のコントローラー アクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="62833-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="62833-131">既定のルート テンプレートは *Startup.Configure* に定義されています。</span><span class="sxs-lookup"><span data-stu-id="62833-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="62833-132">MVC ビューは、次のように、アクションによって提供されるモデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="62833-132">The MVC view uses the model, provided by the action, as follows:</span></span>

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

<span data-ttu-id="62833-133">既定のルートの `{id?}` プレースホルダーが一致しました。</span><span class="sxs-lookup"><span data-stu-id="62833-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="62833-134">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="62833-135">次の MVC ビューのように、ルート プレフィックスが一致するルーティング テンプレートに含まれないものとします。</span><span class="sxs-lookup"><span data-stu-id="62833-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

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

<span data-ttu-id="62833-136">`speakerid` が一致するルートに見つからなかったため、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="62833-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="62833-137">`asp-controller` または `asp-action` のどちらかが指定されていない場合は、`asp-route` 属性の場合と同じ既定の処理に従います。</span><span class="sxs-lookup"><span data-stu-id="62833-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="62833-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="62833-138">asp-route</span></span>

<span data-ttu-id="62833-139">[asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) 属性は、名前付きのルートに直接 URL リンクを作成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62833-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="62833-140">[ルーティング属性](xref:mvc/controllers/routing#attribute-routing)を使用すると、`SpeakerController` に示されているようにルートに名前を付け、その `Evaluations` アクションで使用することができます。</span><span class="sxs-lookup"><span data-stu-id="62833-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="62833-141">次のマークアップでは、`asp-route` 属性が名前付きのルートを参照します。</span><span class="sxs-lookup"><span data-stu-id="62833-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="62833-142">アンカー タグ ヘルパーは URL */Speaker/Evaluations* を使用してそのコントローラー アクションに直接ルートを生成します。</span><span class="sxs-lookup"><span data-stu-id="62833-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="62833-143">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="62833-144">`asp-controller` または `asp-action` が `asp-route` の他に指定されていると、想定と異なるルートが生成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="62833-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="62833-145">ルートの競合を防ぐには、`asp-route` を属性 `asp-controller` および `asp-action` とともに使用してはいけません。</span><span class="sxs-lookup"><span data-stu-id="62833-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="62833-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="62833-146">asp-all-route-data</span></span>

<span data-ttu-id="62833-147">[asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性は、キー/値ペアのディクショナリの作成をサポートします。</span><span class="sxs-lookup"><span data-stu-id="62833-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="62833-148">キーはパラメーターの名前で、値はパラメーターの値です。</span><span class="sxs-lookup"><span data-stu-id="62833-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="62833-149">次の例では、ディクショナリが初期化され、ビューに渡され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="62833-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="62833-150">データがモデルによって渡される場合もあります。</span><span class="sxs-lookup"><span data-stu-id="62833-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="62833-151">上のコードを実行すると、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="62833-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="62833-152">`asp-all-route-data` ディクショナリは、オーバー ロードされた `Evaluations` アクションの要件を満たすクエリ文字列を生成するためにフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="62833-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="62833-153">ディクショナリ内のいずれかのキーがルート パラメーターと一致する場合は、その値がルートで適宜置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="62833-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="62833-154">その他の一致しない値は要求パラメーターとして生成されます。</span><span class="sxs-lookup"><span data-stu-id="62833-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="62833-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="62833-155">asp-fragment</span></span>

<span data-ttu-id="62833-156">[asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) 属性では URL に追加される URL フラグメントが定義されます。</span><span class="sxs-lookup"><span data-stu-id="62833-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="62833-157">アンカー タグ ヘルパーにより、ハッシュ文字 (#) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="62833-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="62833-158">次のマークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="62833-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="62833-159">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="62833-160">ハッシュ タグはクライアント側アプリを構築するときに便利です。</span><span class="sxs-lookup"><span data-stu-id="62833-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="62833-161">たとえば、JavaScript でのマーク付けや検索を簡単にするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="62833-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="62833-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="62833-162">asp-area</span></span>

<span data-ttu-id="62833-163">[asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) 属性は、適切なルートの設定に使用する領域名を設定します。</span><span class="sxs-lookup"><span data-stu-id="62833-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="62833-164">`asp-area` 属性によってどのようにルートの再マップが行われるかの例を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="62833-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-no-locrazor-pages"></a><span data-ttu-id="62833-165">使用 ( Razor ページで)</span><span class="sxs-lookup"><span data-stu-id="62833-165">Usage in Razor Pages</span></span>

<span data-ttu-id="62833-166">Razorページ領域は ASP.NET Core 2.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="62833-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="62833-167">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="62833-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="62833-168">**{プロジェクト名}**</span><span class="sxs-lookup"><span data-stu-id="62833-168">**{Project name}**</span></span>
  * <span data-ttu-id="62833-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="62833-169">**wwwroot**</span></span>
  * <span data-ttu-id="62833-170">**領域**</span><span class="sxs-lookup"><span data-stu-id="62833-170">**Areas**</span></span>
    * <span data-ttu-id="62833-171">**セッション**</span><span class="sxs-lookup"><span data-stu-id="62833-171">**Sessions**</span></span>
      * <span data-ttu-id="62833-172">**ページ**</span><span class="sxs-lookup"><span data-stu-id="62833-172">**Pages**</span></span>
        * <span data-ttu-id="62833-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="62833-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="62833-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="62833-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="62833-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="62833-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="62833-176">**ページ**</span><span class="sxs-lookup"><span data-stu-id="62833-176">**Pages**</span></span>

<span data-ttu-id="62833-177">[*セッション*領域の*インデックス*] ページを参照するマークアップ Razor は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="62833-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="62833-178">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="62833-179">Pages アプリの領域をサポートするには Razor 、で次のいずれかの操作を行い `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="62833-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="62833-180">[互換性バージョン](xref:mvc/compatibility-version)に 2.1 以降を設定します。</span><span class="sxs-lookup"><span data-stu-id="62833-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="62833-181">次のように、[ [ Razor allowareas]](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*)プロパティをに設定します。 `true`</span><span class="sxs-lookup"><span data-stu-id="62833-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="62833-182">MVC の使用法</span><span class="sxs-lookup"><span data-stu-id="62833-182">Usage in MVC</span></span>

<span data-ttu-id="62833-183">次のディレクトリ階層があるとします。</span><span class="sxs-lookup"><span data-stu-id="62833-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="62833-184">**{プロジェクト名}**</span><span class="sxs-lookup"><span data-stu-id="62833-184">**{Project name}**</span></span>
  * <span data-ttu-id="62833-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="62833-185">**wwwroot**</span></span>
  * <span data-ttu-id="62833-186">**領域**</span><span class="sxs-lookup"><span data-stu-id="62833-186">**Areas**</span></span>
    * <span data-ttu-id="62833-187">**ブログ**</span><span class="sxs-lookup"><span data-stu-id="62833-187">**Blogs**</span></span>
      * <span data-ttu-id="62833-188">**コントローラー**</span><span class="sxs-lookup"><span data-stu-id="62833-188">**Controllers**</span></span>
        * <span data-ttu-id="62833-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="62833-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="62833-190">**ビュー**</span><span class="sxs-lookup"><span data-stu-id="62833-190">**Views**</span></span>
        * <span data-ttu-id="62833-191">**Home**</span><span class="sxs-lookup"><span data-stu-id="62833-191">**Home**</span></span>
          * <span data-ttu-id="62833-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="62833-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="62833-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="62833-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="62833-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="62833-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="62833-195">**コントローラー**</span><span class="sxs-lookup"><span data-stu-id="62833-195">**Controllers**</span></span>

<span data-ttu-id="62833-196">`asp-area` を [ブログ] に設定すると、このアンカー タグの関連付けられているコントローラーとビューのルートに、ディレクトリ *Areas/Blogs* のプレフィックスが付けられます。</span><span class="sxs-lookup"><span data-stu-id="62833-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="62833-197">*AboutBlog* ビューを参照するマークアップは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="62833-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="62833-198">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="62833-199">MVC アプリで領域をサポートするには、ルート テンプレートに領域への参照 (存在する場合) が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="62833-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="62833-200">そのテンプレートは、*Startup.Configure* の `routes.MapRoute` メソッド呼び出しの 2 番目のパラメーターで表されます</span><span class="sxs-lookup"><span data-stu-id="62833-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="62833-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="62833-201">asp-protocol</span></span>

<span data-ttu-id="62833-202">[asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) 属性は URL に (`https` などの) プロトコルを指定するためにあります。</span><span class="sxs-lookup"><span data-stu-id="62833-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="62833-203">例:</span><span class="sxs-lookup"><span data-stu-id="62833-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="62833-204">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="62833-205">例ではホスト名が localhost です。</span><span class="sxs-lookup"><span data-stu-id="62833-205">The host name in the example is localhost.</span></span> <span data-ttu-id="62833-206">アンカー タグ ヘルパーは、URL を生成するときに Web サイトのパブリック ドメインを使用します。</span><span class="sxs-lookup"><span data-stu-id="62833-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="62833-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="62833-207">asp-host</span></span>

<span data-ttu-id="62833-208">[asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) 属性は URL のホスト名を指定するためにあります。</span><span class="sxs-lookup"><span data-stu-id="62833-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="62833-209">例:</span><span class="sxs-lookup"><span data-stu-id="62833-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="62833-210">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="62833-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="62833-211">asp-page</span></span>

<span data-ttu-id="62833-212">[Asp ページ](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*)属性は、ページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="62833-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="62833-213">アンカー タグの `href` 属性の値を特定のページに設定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="62833-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="62833-214">ページ名の前にスラッシュ "/" を付けると URL が作成されます。</span><span class="sxs-lookup"><span data-stu-id="62833-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="62833-215">次の例では、出席者ページを参照してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="62833-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="62833-216">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="62833-217">`asp-page` 属性は `asp-route`、`asp-controller`、`asp-action` の各属性と相互に排他的です。</span><span class="sxs-lookup"><span data-stu-id="62833-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="62833-218">ただし、`asp-page` は、次のマークアップのサンプルで示されているように、`asp-route-{value}` とともに使用してルーティングを制御できます。</span><span class="sxs-lookup"><span data-stu-id="62833-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="62833-219">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="62833-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="62833-220">asp-page-handler</span></span>

<span data-ttu-id="62833-221">[Asp ページハンドラー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*)属性は、ページで使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="62833-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="62833-222">特定のページ ハンドラーへのリンクが目的です。</span><span class="sxs-lookup"><span data-stu-id="62833-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="62833-223">次のページ ハンドラーがあるとします。</span><span class="sxs-lookup"><span data-stu-id="62833-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="62833-224">ページ モデルの関連するマークアップが `OnGetProfile` ページ ハンドラーにリンクしています。</span><span class="sxs-lookup"><span data-stu-id="62833-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="62833-225">ページ ハンドラーのメソッド名の `On<Verb>` プレフィックスは `asp-page-handler` 属性値では省略されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="62833-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="62833-226">メソッドが非同期の場合は、`Async` サフィックスも省略されます。</span><span class="sxs-lookup"><span data-stu-id="62833-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="62833-227">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="62833-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="62833-228">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="62833-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
