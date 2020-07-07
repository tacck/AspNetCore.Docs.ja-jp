---
title: ASP.NET Core Blazor のグローバリゼーションおよびローカライズ
author: guardrex
description: 複数のカルチャと言語でユーザーが Razor コンポーネントにアクセスできるようにする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1d24ebe900dfcdeb8b7bcc97f1d212deea9cecae
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402729"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="deb2a-103">ASP.NET Core Blazor のグローバリゼーションおよびローカライズ</span><span class="sxs-lookup"><span data-stu-id="deb2a-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="deb2a-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="deb2a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="deb2a-105">複数のカルチャと言語でユーザーが Razor コンポーネントにアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="deb2a-106">利用できる .NET のグローバリゼーションおよびローカライズのシナリオは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="deb2a-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="deb2a-107">.NET のリソース システム</span><span class="sxs-lookup"><span data-stu-id="deb2a-107">.NET's resources system</span></span>
* <span data-ttu-id="deb2a-108">カルチャ固有の数値と日付の書式</span><span class="sxs-lookup"><span data-stu-id="deb2a-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="deb2a-109">現在、サポートされている ASP.NET Core のローカライズ シナリオは限られています。</span><span class="sxs-lookup"><span data-stu-id="deb2a-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="deb2a-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、Blazor アプリで "*サポートされています*"。</span><span class="sxs-lookup"><span data-stu-id="deb2a-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="deb2a-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>、<xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>、データ注釈のローカライズは ASP.NET Core の MVC シナリオであり、Blazor アプリでは "**サポートされていません**"。</span><span class="sxs-lookup"><span data-stu-id="deb2a-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="deb2a-112">詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="deb2a-113">グローバリゼーション</span><span class="sxs-lookup"><span data-stu-id="deb2a-113">Globalization</span></span>

Blazor<span data-ttu-id="deb2a-114"> の [`@bind`](xref:mvc/views/razor#bind) 機能は、ユーザーの現在のカルチャに基づいて、書式設定を実行し、表示する値を解析します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-114">'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="deb2a-115">現在のカルチャは、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="deb2a-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> は、次のフィールドの型 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="deb2a-117">前のフィールドの型は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="deb2a-117">The preceding field types:</span></span>

* <span data-ttu-id="deb2a-118">適切なブラウザー ベースの書式ルールを使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="deb2a-119">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="deb2a-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="deb2a-120">ブラウザーの実装に基づいてユーザー操作の特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="deb2a-121">次のフィールドの型には、特定の書式設定の要件がありますが、すべての主要なブラウザーでサポートされていないため、Blazor では現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="deb2a-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="deb2a-122">[`@bind`](xref:mvc/views/razor#bind) では、値を解析および書式設定するための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供する `@bind:culture` パラメーターをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="deb2a-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="deb2a-123">`date` および `number` のフィールドの型を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="deb2a-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="deb2a-124">`date` および `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="deb2a-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="deb2a-125">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="deb2a-125">Localization</span></span>

### Blazor WebAssembly

Blazor WebAssembly<span data-ttu-id="deb2a-126"> アプリでは、ユーザーの[言語設定](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)を使用してカルチャが設定されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-126"> apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="deb2a-127">カルチャを明示的に構成するには、`Program.Main` で <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> と <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> を設定します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="deb2a-128">既定では、Blazor WebAssembly アプリに対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="deb2a-129">リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-129">For more information and guidance on controlling the linker's behavior, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="deb2a-130">Blazor で既定で選択されるカルチャは、ほとんどのユーザーにとって十分と考えられますが、ユーザーが優先ロケールを指定する手段を提供することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-130">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="deb2a-131">カルチャ ピッカーを使用した Blazor WebAssembly サンプル アプリについては、[`LocSample`](https://github.com/pranavkm/LocSample) ローカライズのサンプル アプリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-131">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

Blazor Server<span data-ttu-id="deb2a-132"> アプリは、[ローカライズ ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-132"> apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="deb2a-133">ミドルウェアによって、アプリからリソースを要求するユーザーに対して適切なカルチャが選択されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-133">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="deb2a-134">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-134">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="deb2a-135">Cookie</span><span class="sxs-lookup"><span data-stu-id="deb2a-135">Cookies</span></span>](#cookies)
* [<span data-ttu-id="deb2a-136">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="deb2a-136">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="deb2a-137">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-137">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="deb2a-138">クッキー</span><span class="sxs-lookup"><span data-stu-id="deb2a-138">Cookies</span></span>

<span data-ttu-id="deb2a-139">ローカライズ カルチャの Cookie では、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-139">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="deb2a-140">ローカライズ ミドルウェアでは、後続の要求で Cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-140">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="deb2a-141">Cookie を使用すると、WebSocket 接続によってカルチャを正しく伝達できることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="deb2a-141">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="deb2a-142">ローカライズ スキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが WebSocket を使用できない可能性があるため、カルチャを保持できません。</span><span class="sxs-lookup"><span data-stu-id="deb2a-142">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="deb2a-143">したがって、ローカライズ カルチャの Cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="deb2a-143">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="deb2a-144">カルチャがローカライズ Cookie で保持されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-144">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="deb2a-145">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズ スキームが既にある場合は、アプリの既存のローカライズ インフラストラクチャを引き続き使用し、アプリのスキーム内にローカライズ カルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-145">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="deb2a-146">次の例では、ローカライズ ミドルウェアによって読み取ることができる Cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-146">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="deb2a-147">`Pages/_Host.cshtml` ファイルの開いている `<body>` タグのすぐ下に、Razor 式を作成します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-147">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="deb2a-148">ローカライズは、次の一連のイベントでアプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-148">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="deb2a-149">ブラウザーによって、アプリに最初の HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-149">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="deb2a-150">カルチャは、ローカライズ ミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-150">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="deb2a-151">`_Host` ページ (`_Host.cshtml`) の Razor 式によって、応答の一部として Cookie にカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-151">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="deb2a-152">ブラウザーによって、WebSocket 接続が開かれ、対話型の Blazor Server セッションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-152">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="deb2a-153">ローカライズ ミドルウェアによって、Cookie が読み取られ、カルチャが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-153">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="deb2a-154">Blazor Server セッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-154">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="deb2a-155">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="deb2a-155">Provide UI to choose the culture</span></span>

<span data-ttu-id="deb2a-156">ユーザーがカルチャを選択できるように UI を提供するには、"*リダイレクト ベースのアプローチ*" をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="deb2a-156">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="deb2a-157">このプロセスは、セキュリティで保護されたリソースにユーザーがアクセスしようとすると Web アプリで発生する処理に似ています。</span><span class="sxs-lookup"><span data-stu-id="deb2a-157">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="deb2a-158">ユーザーはサインイン ページにリダイレクトされ、元のリソースに再びリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-158">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="deb2a-159">アプリでは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-159">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="deb2a-160">コントローラーによって、ユーザーが選択したカルチャが Cookie に設定され、ユーザーは元の URI にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-160">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="deb2a-161">Cookie にユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するように、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-161">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="deb2a-162"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> アクションの結果を使用して、オープン リダイレクト攻撃を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="deb2a-162">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="deb2a-163">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="deb2a-163">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="deb2a-164">アプリがコントローラー アクションを処理するように構成されていない場合:</span><span class="sxs-lookup"><span data-stu-id="deb2a-164">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="deb2a-165">次のように `Startup.ConfigureServices` のサービス コレクションに MVC サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-165">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="deb2a-166">次のように `Startup.Configure` にコントローラーのエンドポイント ルーティングを追加します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-166">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="deb2a-167">次のコンポーネントでは、ユーザーがカルチャを選択したときに、最初のリダイレクトを実行する方法の例を示します。</span><span class="sxs-lookup"><span data-stu-id="deb2a-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="deb2a-168">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="deb2a-168">Additional resources</span></span>

* <xref:fundamentals/localization>
