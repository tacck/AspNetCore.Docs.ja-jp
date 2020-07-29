---
title: ASP.NET Core のグローバリゼーションおよびローカリゼーション
author: rick-anderson
description: ASP.NET Core がコンテンツをさまざまな言語と文化にローカライズするために提供するサービスとミドルウェアについて説明します。
ms.author: riande
ms.date: 11/30/2019
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: fundamentals/localization
ms.openlocfilehash: 412cd7a39a0eed6800e15d235102ed367da5f746
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793476"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="882b0-103">ASP.NET Core のグローバリゼーションおよびローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="882b0-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="882b0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="882b0-105">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="882b0-106">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="882b0-107">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="882b0-108">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="882b0-109">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="882b0-110">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="882b0-111">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="882b0-112">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="882b0-112">App localization involves the following:</span></span>

1. <span data-ttu-id="882b0-113">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="882b0-114">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="882b0-115">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="882b0-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="882b0-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="882b0-117">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-117">Make the app's content localizable</span></span>

<span data-ttu-id="882b0-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="882b0-119">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="882b0-120">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="882b0-121">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="882b0-122">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="882b0-123">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="882b0-124">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="882b0-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="882b0-125">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="882b0-126">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="882b0-127">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="882b0-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="882b0-128">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="882b0-129">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="882b0-130">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="882b0-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="882b0-131">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="882b0-132">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="882b0-133">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="882b0-134">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-135">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="882b0-136">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="882b0-137">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="882b0-138">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="882b0-139">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="882b0-140">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="882b0-141">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-141">View localization</span></span>

<span data-ttu-id="882b0-142">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="882b0-143">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="882b0-144">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="882b0-145">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="882b0-146">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="882b0-147">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、[Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so [Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="882b0-148">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="882b0-149">次の [Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-149">Consider the following [Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="882b0-150">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="882b0-151">Key</span><span class="sxs-lookup"><span data-stu-id="882b0-151">Key</span></span> | <span data-ttu-id="882b0-152">[値]</span><span class="sxs-lookup"><span data-stu-id="882b0-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="882b0-153">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="882b0-154">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-155">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="882b0-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="882b0-156">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-156">DataAnnotations localization</span></span>

<span data-ttu-id="882b0-157">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="882b0-158">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="882b0-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="882b0-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="882b0-161">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="882b0-162">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="882b0-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="882b0-163">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="882b0-164">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="882b0-165">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="882b0-166">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="882b0-167">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="882b0-168">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="882b0-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="882b0-169">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="882b0-170">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="882b0-171">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="882b0-172">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="882b0-173">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="882b0-174">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="882b0-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="882b0-175">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="882b0-176">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="882b0-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="882b0-177">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="882b0-178">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="882b0-178">Resource files</span></span>

<span data-ttu-id="882b0-179">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="882b0-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="882b0-180">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="882b0-181">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="882b0-182">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="882b0-183">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="882b0-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="882b0-184">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="882b0-187">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="882b0-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="882b0-189">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="882b0-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="882b0-191">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="882b0-193">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-193">Resource file naming</span></span>

<span data-ttu-id="882b0-194">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="882b0-195">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="882b0-196">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-197">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="882b0-198">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="882b0-199">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-200">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="882b0-201">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-202">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="882b0-203">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-204">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="882b0-205">リソース名</span><span class="sxs-lookup"><span data-stu-id="882b0-205">Resource name</span></span> | <span data-ttu-id="882b0-206">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="882b0-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="882b0-208">ドット</span><span class="sxs-lookup"><span data-stu-id="882b0-208">Dot</span></span>  |
| <span data-ttu-id="882b0-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="882b0-210">パス</span><span class="sxs-lookup"><span data-stu-id="882b0-210">Path</span></span> |

<span data-ttu-id="882b0-211">[Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="882b0-211">Resource files using `@inject IViewLocalizer` in [Razor views follow a similar pattern.</span></span> <span data-ttu-id="882b0-212">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="882b0-213">[Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="882b0-213">[Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="882b0-214">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="882b0-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="882b0-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="882b0-217">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="882b0-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="882b0-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="882b0-219">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="882b0-220">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="882b0-221">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="882b0-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="882b0-222">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="882b0-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="882b0-223">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-223">Localization does not work by default.</span></span>
* <span data-ttu-id="882b0-224">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="882b0-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="882b0-225">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="882b0-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="882b0-226">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="882b0-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="882b0-227">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="882b0-228">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="882b0-228">Culture fallback behavior</span></span>

<span data-ttu-id="882b0-229">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="882b0-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="882b0-230">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="882b0-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="882b0-231">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="882b0-232">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="882b0-233">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="882b0-234">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="882b0-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="882b0-235">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="882b0-236">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="882b0-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="882b0-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="882b0-239">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="882b0-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="882b0-240">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="882b0-241">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="882b0-242">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="882b0-243">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="882b0-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="882b0-244">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="882b0-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="882b0-245">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="882b0-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="882b0-246">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="882b0-247">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="882b0-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="882b0-248">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="882b0-249">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="882b0-249">Add other cultures</span></span>

<span data-ttu-id="882b0-250">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="882b0-251">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="882b0-252">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="882b0-253">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="882b0-254">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="882b0-254">Configure localization</span></span>

<span data-ttu-id="882b0-255">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="882b0-256">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="882b0-257">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="882b0-258">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="882b0-259">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="882b0-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="882b0-260">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="882b0-261">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="882b0-262">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="882b0-262">Localization middleware</span></span>

<span data-ttu-id="882b0-263">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="882b0-264">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="882b0-265">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="882b0-266">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="882b0-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="882b0-267">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="882b0-268">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="882b0-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="882b0-269">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="882b0-270">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="882b0-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="882b0-271">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="882b0-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="882b0-273">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="882b0-274">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="882b0-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="882b0-275">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="882b0-276">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="882b0-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="882b0-277">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="882b0-278">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="882b0-279">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="882b0-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="882b0-281">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="882b0-282">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="882b0-283">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="882b0-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="882b0-284">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="882b0-285">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="882b0-286">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="882b0-287">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="882b0-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="882b0-288">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="882b0-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="882b0-289">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="882b0-290">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="882b0-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="882b0-291">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="882b0-292">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="882b0-293">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="882b0-294">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-294">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="882b0-296">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="882b0-297">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="882b0-298">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-298">Add the language.</span></span>

1. <span data-ttu-id="882b0-299">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="882b0-300">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-300">Use a custom provider</span></span>

<span data-ttu-id="882b0-301">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="882b0-302">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="882b0-303">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="882b0-304">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="882b0-305">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-305">Set the culture programmatically</span></span>

<span data-ttu-id="882b0-306">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="882b0-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="882b0-307">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="882b0-308">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="882b0-309">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="882b0-310">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="882b0-311">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して [Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a [Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="882b0-312">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="882b0-312">Model binding route data and query strings</span></span>

<span data-ttu-id="882b0-313">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="882b0-314">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="882b0-314">Globalization and localization terms</span></span>

<span data-ttu-id="882b0-315">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="882b0-316">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="882b0-317">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="882b0-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="882b0-318">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="882b0-319">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="882b0-320">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="882b0-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="882b0-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="882b0-322">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-322">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="882b0-323">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="882b0-324">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="882b0-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="882b0-325">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="882b0-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="882b0-326">用語:</span><span class="sxs-lookup"><span data-stu-id="882b0-326">Terms:</span></span>

* <span data-ttu-id="882b0-327">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="882b0-328">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="882b0-329">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="882b0-330">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="882b0-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="882b0-331">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="882b0-332">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="882b0-332">(for example "en", "es")</span></span>
* <span data-ttu-id="882b0-333">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="882b0-334">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="882b0-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="882b0-335">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="882b0-336">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="882b0-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="882b0-337">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="882b0-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="882b0-338">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="882b0-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="882b0-339">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="882b0-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="882b0-340">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="882b0-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="882b0-341">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="882b0-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="882b0-342">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="882b0-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="882b0-343">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="882b0-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="882b0-344">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="882b0-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="882b0-345">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="882b0-346">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="882b0-347">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="882b0-348">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="882b0-349">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="882b0-350">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="882b0-351">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="882b0-352">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="882b0-352">App localization involves the following:</span></span>

1. <span data-ttu-id="882b0-353">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="882b0-354">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="882b0-355">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="882b0-356">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="882b0-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="882b0-357">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-357">Make the app's content localizable</span></span>

<span data-ttu-id="882b0-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="882b0-359">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="882b0-360">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="882b0-361">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="882b0-362">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="882b0-363">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="882b0-364">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="882b0-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="882b0-365">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="882b0-366">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="882b0-367">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="882b0-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="882b0-368">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="882b0-369">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="882b0-370">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="882b0-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="882b0-371">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="882b0-372">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="882b0-373">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="882b0-374">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-375">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="882b0-376">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="882b0-377">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="882b0-378">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="882b0-379">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="882b0-380">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="882b0-381">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-381">View localization</span></span>

<span data-ttu-id="882b0-382">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="882b0-383">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="882b0-384">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="882b0-385">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="882b0-386">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="882b0-387">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、[Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so [Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="882b0-388">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="882b0-389">次の [Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-389">Consider the following [Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="882b0-390">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="882b0-391">Key</span><span class="sxs-lookup"><span data-stu-id="882b0-391">Key</span></span> | <span data-ttu-id="882b0-392">[値]</span><span class="sxs-lookup"><span data-stu-id="882b0-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="882b0-393">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="882b0-394">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-395">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="882b0-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="882b0-396">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-396">DataAnnotations localization</span></span>

<span data-ttu-id="882b0-397">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="882b0-398">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="882b0-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="882b0-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="882b0-401">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="882b0-402">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="882b0-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="882b0-403">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="882b0-404">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="882b0-405">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="882b0-406">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="882b0-407">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="882b0-408">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="882b0-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="882b0-409">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="882b0-410">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="882b0-411">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="882b0-412">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="882b0-413">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="882b0-414">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="882b0-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="882b0-415">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="882b0-416">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="882b0-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="882b0-417">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="882b0-418">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="882b0-418">Resource files</span></span>

<span data-ttu-id="882b0-419">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="882b0-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="882b0-420">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="882b0-421">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="882b0-422">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="882b0-423">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="882b0-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="882b0-424">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="882b0-427">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="882b0-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="882b0-429">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="882b0-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="882b0-431">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="882b0-433">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-433">Resource file naming</span></span>

<span data-ttu-id="882b0-434">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="882b0-435">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="882b0-436">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-437">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="882b0-438">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="882b0-439">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-440">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="882b0-441">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-442">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="882b0-443">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-444">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="882b0-445">リソース名</span><span class="sxs-lookup"><span data-stu-id="882b0-445">Resource name</span></span> | <span data-ttu-id="882b0-446">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="882b0-447">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="882b0-448">ドット</span><span class="sxs-lookup"><span data-stu-id="882b0-448">Dot</span></span>  |
| <span data-ttu-id="882b0-449">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="882b0-450">パス</span><span class="sxs-lookup"><span data-stu-id="882b0-450">Path</span></span> |

<span data-ttu-id="882b0-451">[Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="882b0-451">Resource files using `@inject IViewLocalizer` in [Razor views follow a similar pattern.</span></span> <span data-ttu-id="882b0-452">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="882b0-453">[Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="882b0-453">[Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="882b0-454">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="882b0-455">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="882b0-456">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="882b0-457">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="882b0-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="882b0-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="882b0-459">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-459">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="882b0-460">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="882b0-461">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="882b0-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="882b0-462">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="882b0-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="882b0-463">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-463">Localization does not work by default.</span></span>
* <span data-ttu-id="882b0-464">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="882b0-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="882b0-465">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="882b0-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="882b0-466">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="882b0-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="882b0-467">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="882b0-468">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="882b0-468">Culture fallback behavior</span></span>

<span data-ttu-id="882b0-469">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="882b0-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="882b0-470">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="882b0-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="882b0-471">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="882b0-472">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="882b0-473">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="882b0-474">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="882b0-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="882b0-475">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="882b0-476">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="882b0-477">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="882b0-478">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="882b0-479">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="882b0-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="882b0-480">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="882b0-481">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="882b0-482">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="882b0-483">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="882b0-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="882b0-484">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="882b0-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="882b0-485">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="882b0-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="882b0-486">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="882b0-487">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="882b0-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="882b0-488">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="882b0-489">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="882b0-489">Add other cultures</span></span>

<span data-ttu-id="882b0-490">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="882b0-491">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="882b0-492">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="882b0-493">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="882b0-494">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="882b0-494">Configure localization</span></span>

<span data-ttu-id="882b0-495">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="882b0-496">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="882b0-497">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="882b0-498">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="882b0-499">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="882b0-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="882b0-500">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="882b0-501">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="882b0-502">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="882b0-502">Localization middleware</span></span>

<span data-ttu-id="882b0-503">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="882b0-504">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="882b0-505">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="882b0-506">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="882b0-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="882b0-507">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="882b0-508">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="882b0-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="882b0-509">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="882b0-510">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="882b0-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="882b0-511">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="882b0-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="882b0-513">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-513">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="882b0-514">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="882b0-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="882b0-515">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="882b0-516">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="882b0-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="882b0-517">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="882b0-518">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="882b0-519">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="882b0-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="882b0-521">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="882b0-522">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="882b0-523">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="882b0-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="882b0-524">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="882b0-525">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="882b0-526">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="882b0-527">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="882b0-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="882b0-528">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="882b0-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="882b0-529">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="882b0-530">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="882b0-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="882b0-531">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="882b0-532">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="882b0-533">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="882b0-534">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-534">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="882b0-536">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="882b0-537">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="882b0-538">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-538">Add the language.</span></span>

1. <span data-ttu-id="882b0-539">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="882b0-540">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-540">Use a custom provider</span></span>

<span data-ttu-id="882b0-541">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="882b0-542">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="882b0-543">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="882b0-544">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="882b0-545">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-545">Set the culture programmatically</span></span>

<span data-ttu-id="882b0-546">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="882b0-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="882b0-547">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="882b0-548">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="882b0-549">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="882b0-550">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="882b0-551">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して [Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a [Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="882b0-552">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="882b0-552">Model binding route data and query strings</span></span>

<span data-ttu-id="882b0-553">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="882b0-554">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="882b0-554">Globalization and localization terms</span></span>

<span data-ttu-id="882b0-555">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="882b0-556">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="882b0-557">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="882b0-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="882b0-558">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="882b0-559">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="882b0-560">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="882b0-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="882b0-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="882b0-562">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-562">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="882b0-563">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="882b0-564">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="882b0-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="882b0-565">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="882b0-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="882b0-566">用語:</span><span class="sxs-lookup"><span data-stu-id="882b0-566">Terms:</span></span>

* <span data-ttu-id="882b0-567">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="882b0-568">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="882b0-569">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="882b0-570">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="882b0-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="882b0-571">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="882b0-572">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="882b0-572">(for example "en", "es")</span></span>
* <span data-ttu-id="882b0-573">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="882b0-574">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="882b0-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="882b0-575">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="882b0-576">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="882b0-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="882b0-577">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="882b0-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="882b0-578">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="882b0-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="882b0-579">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="882b0-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="882b0-580">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="882b0-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="882b0-581">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="882b0-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="882b0-582">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="882b0-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="882b0-583">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="882b0-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="882b0-584">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="882b0-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="882b0-585">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="882b0-586">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="882b0-587">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="882b0-588">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="882b0-589">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="882b0-590">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="882b0-591">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="882b0-592">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="882b0-592">App localization involves the following:</span></span>

1. <span data-ttu-id="882b0-593">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="882b0-594">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="882b0-595">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="882b0-596">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="882b0-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="882b0-597">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="882b0-597">Make the app's content localizable</span></span>

<span data-ttu-id="882b0-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="882b0-599">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="882b0-600">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="882b0-601">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="882b0-602">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="882b0-603">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="882b0-604">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="882b0-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="882b0-605">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="882b0-606">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="882b0-607">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="882b0-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="882b0-608">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="882b0-609">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="882b0-610">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="882b0-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="882b0-611">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="882b0-612">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="882b0-613">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="882b0-614">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-615">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="882b0-616">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="882b0-617">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="882b0-618">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="882b0-619">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="882b0-620">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="882b0-621">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-621">View localization</span></span>

<span data-ttu-id="882b0-622">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="882b0-623">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="882b0-624">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="882b0-625">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="882b0-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="882b0-626">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="882b0-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="882b0-627">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、[Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so [Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="882b0-628">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="882b0-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="882b0-629">次の [Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-629">Consider the following [Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="882b0-630">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="882b0-631">Key</span><span class="sxs-lookup"><span data-stu-id="882b0-631">Key</span></span> | <span data-ttu-id="882b0-632">[値]</span><span class="sxs-lookup"><span data-stu-id="882b0-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="882b0-633">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="882b0-634">一般に、HTML ではなく、テキストのみをローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="882b0-635">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="882b0-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="882b0-636">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="882b0-636">DataAnnotations localization</span></span>

<span data-ttu-id="882b0-637">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="882b0-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="882b0-638">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="882b0-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="882b0-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="882b0-641">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="882b0-642">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="882b0-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="882b0-643">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="882b0-644">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="882b0-645">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="882b0-646">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="882b0-647">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="882b0-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="882b0-648">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="882b0-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="882b0-649">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="882b0-650">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="882b0-651">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="882b0-652">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="882b0-653">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="882b0-654">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="882b0-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="882b0-655">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="882b0-656">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="882b0-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="882b0-657">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="882b0-658">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="882b0-658">Resource files</span></span>

<span data-ttu-id="882b0-659">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="882b0-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="882b0-660">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="882b0-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="882b0-661">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="882b0-662">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="882b0-663">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="882b0-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="882b0-664">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

1. <span data-ttu-id="882b0-667">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="882b0-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

1. <span data-ttu-id="882b0-669">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="882b0-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

   <span data-ttu-id="882b0-671">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="882b0-673">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-673">Resource file naming</span></span>

<span data-ttu-id="882b0-674">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="882b0-675">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="882b0-676">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-677">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="882b0-678">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="882b0-679">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-680">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="882b0-681">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="882b0-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-682">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="882b0-683">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="882b0-684">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="882b0-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="882b0-685">リソース名</span><span class="sxs-lookup"><span data-stu-id="882b0-685">Resource name</span></span> | <span data-ttu-id="882b0-686">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="882b0-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="882b0-687">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="882b0-688">ドット</span><span class="sxs-lookup"><span data-stu-id="882b0-688">Dot</span></span>  |
| <span data-ttu-id="882b0-689">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="882b0-690">パス</span><span class="sxs-lookup"><span data-stu-id="882b0-690">Path</span></span> |

<span data-ttu-id="882b0-691">[Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="882b0-691">Resource files using `@inject IViewLocalizer` in [Razor views follow a similar pattern.</span></span> <span data-ttu-id="882b0-692">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="882b0-693">[Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="882b0-693">[Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="882b0-694">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="882b0-695">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="882b0-696">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="882b0-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="882b0-697">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="882b0-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="882b0-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="882b0-699">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-699">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="882b0-700">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="882b0-701">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="882b0-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="882b0-702">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="882b0-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="882b0-703">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-703">Localization does not work by default.</span></span>
* <span data-ttu-id="882b0-704">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="882b0-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="882b0-705">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="882b0-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="882b0-706">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="882b0-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="882b0-707">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="882b0-708">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="882b0-708">Culture fallback behavior</span></span>

<span data-ttu-id="882b0-709">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="882b0-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="882b0-710">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="882b0-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="882b0-711">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="882b0-712">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="882b0-713">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="882b0-714">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="882b0-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="882b0-715">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="882b0-716">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="882b0-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="882b0-717">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="882b0-718">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="882b0-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="882b0-719">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="882b0-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="882b0-720">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="882b0-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="882b0-721">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="882b0-722">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="882b0-723">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="882b0-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="882b0-724">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="882b0-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="882b0-725">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="882b0-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="882b0-726">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="882b0-727">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="882b0-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="882b0-728">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="882b0-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="882b0-729">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="882b0-729">Add other cultures</span></span>

<span data-ttu-id="882b0-730">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="882b0-731">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="882b0-732">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="882b0-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="882b0-733">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="882b0-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="882b0-734">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="882b0-734">Configure localization</span></span>

<span data-ttu-id="882b0-735">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="882b0-736">`AddLocalization` は、ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="882b0-737">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="882b0-738">`AddViewLocalization` は、ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="882b0-739">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="882b0-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="882b0-740">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="882b0-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="882b0-741">`AddDataAnnotationsLocalization` は、`IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="882b0-742">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="882b0-742">Localization middleware</span></span>

<span data-ttu-id="882b0-743">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="882b0-744">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="882b0-745">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="882b0-746">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="882b0-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="882b0-747">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="882b0-748">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="882b0-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="882b0-749">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="882b0-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="882b0-750">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="882b0-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="882b0-751">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="882b0-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="882b0-753">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-753">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="882b0-754">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="882b0-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="882b0-755">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="882b0-756">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="882b0-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="882b0-757">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="882b0-758">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="882b0-759">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="882b0-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="882b0-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="882b0-761">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="882b0-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="882b0-762">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="882b0-763">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="882b0-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="882b0-764">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="882b0-765">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="882b0-766">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="882b0-767">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="882b0-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="882b0-768">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="882b0-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="882b0-769">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="882b0-770">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="882b0-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="882b0-771">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="882b0-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="882b0-772">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="882b0-773">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="882b0-774">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-774">Tap **Languages**.</span></span>

   ![インターネット オプション](localization/_static/lang.png)

1. <span data-ttu-id="882b0-776">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="882b0-777">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="882b0-778">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="882b0-778">Add the language.</span></span>

1. <span data-ttu-id="882b0-779">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="882b0-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="882b0-780">Content-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="882b0-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="882b0-781">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) エンティティ ヘッダーは、</span><span class="sxs-lookup"><span data-stu-id="882b0-781">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="882b0-782">対象ユーザー用に想定した言語を説明するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="882b0-783">ユーザーが、そのユーザー独自の優先言語に従って区別することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="882b0-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="882b0-784">エンティティ ヘッダーは、HTTP 要求と応答の両方で使用されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="882b0-785">プロパティ `ApplyCurrentCultureToResponseHeaders` を設定することによって `Content-Language` ヘッダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="882b0-786">`Content-Language` ヘッダーを追加すると、</span><span class="sxs-lookup"><span data-stu-id="882b0-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="882b0-787">RequestLocalizationMiddleware で `CurrentUICulture` を使って `Content-Language` を設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="882b0-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="882b0-788">応答ヘッダーの `Content-Language` を明示的に設定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="882b0-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="882b0-789">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="882b0-789">Use a custom provider</span></span>

<span data-ttu-id="882b0-790">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="882b0-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="882b0-791">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="882b0-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="882b0-792">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="882b0-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="882b0-793">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="882b0-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="882b0-794">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="882b0-794">Set the culture programmatically</span></span>

<span data-ttu-id="882b0-795">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="882b0-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="882b0-796">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="882b0-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="882b0-797">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="882b0-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="882b0-798">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="882b0-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="882b0-799">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="882b0-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="882b0-800">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して [Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="882b0-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a [Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="882b0-801">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="882b0-801">Model binding route data and query strings</span></span>

<span data-ttu-id="882b0-802">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="882b0-803">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="882b0-803">Globalization and localization terms</span></span>

<span data-ttu-id="882b0-804">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="882b0-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="882b0-805">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="882b0-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="882b0-806">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="882b0-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="882b0-807">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="882b0-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="882b0-808">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="882b0-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="882b0-809">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="882b0-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="882b0-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="882b0-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="882b0-811">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882b0-811">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="882b0-812">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="882b0-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="882b0-813">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="882b0-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="882b0-814">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="882b0-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="882b0-815">用語:</span><span class="sxs-lookup"><span data-stu-id="882b0-815">Terms:</span></span>

* <span data-ttu-id="882b0-816">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="882b0-817">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="882b0-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="882b0-818">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="882b0-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="882b0-819">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="882b0-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="882b0-820">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="882b0-821">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="882b0-821">(for example "en", "es")</span></span>
* <span data-ttu-id="882b0-822">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="882b0-823">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="882b0-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="882b0-824">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="882b0-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="882b0-825">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="882b0-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="882b0-826">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="882b0-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="882b0-827">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="882b0-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="882b0-828">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="882b0-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="882b0-829">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="882b0-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="882b0-830">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="882b0-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="882b0-831">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="882b0-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="882b0-832">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="882b0-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
