---
title: 'ASP.NET Core :::no-loc(Blazor)::: のグローバリゼーションおよびローカライズ'
author: guardrex
description: '複数のカルチャと言語でユーザーが :::no-loc(Razor)::: コンポーネントにアクセスできるようにする方法について学習します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
uid: blazor/globalization-localization
ms.openlocfilehash: 4345dd8525c2e72aaddc8e45a4fd4d9bfdd63040
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326531"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="8bda3-103">ASP.NET Core :::no-loc(Blazor)::: のグローバリゼーションおよびローカライズ</span><span class="sxs-lookup"><span data-stu-id="8bda3-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="8bda3-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8bda3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8bda3-105">複数のカルチャと言語でユーザーが :::no-loc(Razor)::: コンポーネントにアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="8bda3-106">利用できる .NET のグローバリゼーションおよびローカライズのシナリオは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8bda3-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="8bda3-107">.NET のリソース システム</span><span class="sxs-lookup"><span data-stu-id="8bda3-107">.NET's resources system</span></span>
* <span data-ttu-id="8bda3-108">カルチャ固有の数値と日付の書式</span><span class="sxs-lookup"><span data-stu-id="8bda3-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="8bda3-109">現在、サポートされている ASP.NET Core のローカライズ シナリオは限られています。</span><span class="sxs-lookup"><span data-stu-id="8bda3-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="8bda3-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、:::no-loc(Blazor)::: アプリで " *サポートされています* "。</span><span class="sxs-lookup"><span data-stu-id="8bda3-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="8bda3-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、<xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>、データ注釈のローカライズは ASP.NET Core の MVC シナリオであり、:::no-loc(Blazor)::: アプリでは " **サポートされていません** "。</span><span class="sxs-lookup"><span data-stu-id="8bda3-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="8bda3-112">詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="8bda3-113">グローバリゼーション</span><span class="sxs-lookup"><span data-stu-id="8bda3-113">Globalization</span></span>

<span data-ttu-id="8bda3-114">:::no-loc(Blazor)::: の [`@bind`](xref:mvc/views/razor#bind) 機能は、ユーザーの現在のカルチャに基づいて、書式設定を実行し、表示する値を解析します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="8bda3-115">現在のカルチャは、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="8bda3-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> は、次のフィールドの型 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="8bda3-117">前のフィールドの型は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8bda3-117">The preceding field types:</span></span>

* <span data-ttu-id="8bda3-118">適切なブラウザー ベースの書式ルールを使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="8bda3-119">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8bda3-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="8bda3-120">ブラウザーの実装に基づいてユーザー操作の特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="8bda3-121">次のフィールドの型には、特定の書式設定の要件がありますが、すべての主要なブラウザーでサポートされていないため、:::no-loc(Blazor)::: では現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8bda3-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="8bda3-122">[`@bind`](xref:mvc/views/razor#bind) では、値を解析および書式設定するための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供する `@bind:culture` パラメーターをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8bda3-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="8bda3-123">`date` および `number` のフィールドの型を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8bda3-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="8bda3-124">`date` および `number` には、必要なカルチャを提供する :::no-loc(Blazor)::: サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="8bda3-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="8bda3-125">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="8bda3-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="8bda3-126">:::no-loc(Blazor WebAssembly)::: アプリでは、ユーザーの[言語設定](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)を使用してカルチャが設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="8bda3-127">カルチャを明示的に構成するには、`Program.Main` で <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> と <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> を設定します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8bda3-128">既定では、:::no-loc(Blazor WebAssembly)::: には、ユーザーのカルチャで日付や通貨などの値を表示するために必要な最小限のグローバリゼーション リソースがあります。</span><span class="sxs-lookup"><span data-stu-id="8bda3-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="8bda3-129">カルチャの動的な変更をサポートする必要があるアプリケーションでは、プロジェクト ファイルで `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bda3-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="8bda3-130">:::no-loc(Blazor WebAssembly)::: は、`:::no-loc(Blazor):::.start` に渡されるオプションを使用して、特定のアプリケーション カルチャを使用して起動するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="8bda3-131">たとえば、次のサンプルでは、`en-GB` カルチャを使用して起動するように構成されたアプリが示されています。</span><span class="sxs-lookup"><span data-stu-id="8bda3-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="8bda3-132">`applicationCulture` の値は、[BCP-47 言語タグ形式](https://tools.ietf.org/html/bcp47)に準拠している必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bda3-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="8bda3-133">アプリがローカライズを必要としない場合は、`en-US` カルチャに基づくインバリアント カルチャをサポートするようにアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8bda3-134">既定では、:::no-loc(Blazor WebAssembly)::: アプリに対する中間言語 (IL) リンカーの構成によって、明示的に要求されたロケールを除き、国際化情報が削除されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="8bda3-135">詳細については、「<xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="8bda3-136">:::no-loc(Blazor)::: で既定で選択されるカルチャは、ほとんどのユーザーにとって十分と考えられますが、ユーザーが優先ロケールを指定する手段を提供することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="8bda3-137">カルチャ ピッカーを使用した :::no-loc(Blazor WebAssembly)::: サンプル アプリについては、[`LocSample`](https://github.com/pranavkm/LocSample) ローカライズのサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="8bda3-138">:::no-loc(Blazor Server)::: アプリは、[ローカライズ ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="8bda3-139">ミドルウェアによって、アプリからリソースを要求するユーザーに対して適切なカルチャが選択されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="8bda3-140">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="8bda3-141">[:::no-loc(Cookie)::: ](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="8bda3-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="8bda3-142">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="8bda3-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="8bda3-143">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="8bda3-144">:::no-loc(Cookie):::s</span><span class="sxs-lookup"><span data-stu-id="8bda3-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="8bda3-145">ローカライズ カルチャの :::no-loc(cookie)::: では、ユーザーのカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="8bda3-146">ローカライズ ミドルウェアでは、後続の要求で :::no-loc(cookie)::: を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="8bda3-147">:::no-loc(cookie)::: を使用すると、WebSocket 接続によってカルチャを正しく伝達できることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="8bda3-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="8bda3-148">ローカライズ スキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが WebSocket を使用できない可能性があるため、カルチャを保持できません。</span><span class="sxs-lookup"><span data-stu-id="8bda3-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="8bda3-149">したがって、ローカライズ カルチャの :::no-loc(cookie)::: を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8bda3-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="8bda3-150">カルチャがローカライズ :::no-loc(cookie)::: で保持されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="8bda3-151">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズ スキームが既にある場合は、アプリの既存のローカライズ インフラストラクチャを引き続き使用し、アプリのスキーム内にローカライズ カルチャ :::no-loc(cookie)::: を設定します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="8bda3-152">次の例では、ローカライズ ミドルウェアによって読み取ることができる :::no-loc(cookie)::: で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="8bda3-153">`Pages/_Host.cshtml` ファイルの開いている `<body>` タグのすぐ下に、:::no-loc(Razor)::: 式を作成します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="8bda3-154">ローカライズは、次の一連のイベントでアプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="8bda3-155">ブラウザーによって、アプリに最初の HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="8bda3-156">カルチャは、ローカライズ ミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="8bda3-157">`_Host` ページ (`_Host.cshtml`) の :::no-loc(Razor)::: 式によって、応答の一部として :::no-loc(cookie)::: にカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="8bda3-158">ブラウザーによって、WebSocket 接続が開かれ、対話型の :::no-loc(Blazor Server)::: セッションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="8bda3-159">ローカライズ ミドルウェアによって、:::no-loc(cookie)::: が読み取られ、カルチャが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="8bda3-160">:::no-loc(Blazor Server)::: セッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="8bda3-161">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="8bda3-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="8bda3-162">ユーザーがカルチャを選択できるように UI を提供するには、" *リダイレクト ベースのアプローチ* " をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8bda3-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="8bda3-163">このプロセスは、セキュリティで保護されたリソースにユーザーがアクセスしようとすると Web アプリで発生する処理に似ています。</span><span class="sxs-lookup"><span data-stu-id="8bda3-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="8bda3-164">ユーザーはサインイン ページにリダイレクトされ、元のリソースに再びリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="8bda3-165">アプリでは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="8bda3-166">コントローラーによって、ユーザーが選択したカルチャが :::no-loc(cookie)::: に設定され、ユーザーは元の URI にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-166">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="8bda3-167">:::no-loc(cookie)::: にユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するように、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-167">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="8bda3-168"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> アクションの結果を使用して、オープン リダイレクト攻撃を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="8bda3-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="8bda3-169">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bda3-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="8bda3-170">アプリがコントローラー アクションを処理するように構成されていない場合:</span><span class="sxs-lookup"><span data-stu-id="8bda3-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="8bda3-171">次のように `Startup.ConfigureServices` のサービス コレクションに MVC サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="8bda3-172">次のように `Startup.Configure` にコントローラーのエンドポイント ルーティングを追加します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="8bda3-173">次のコンポーネントでは、ユーザーがカルチャを選択したときに、最初のリダイレクトを実行する方法の例を示します。</span><span class="sxs-lookup"><span data-stu-id="8bda3-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="8bda3-174">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8bda3-174">Additional resources</span></span>

* <xref:fundamentals/localization>
