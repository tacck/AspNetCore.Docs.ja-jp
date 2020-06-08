---
title: 'title:ASP.NET Core でのグローバリゼーションとローカライズ author: rick-anderson description:ASP.NET Core がコンテンツをさまざまな言語と文化にローカライズするために提供するサービスとミドルウェアについて説明します。'
author: rick-anderson
description: 'ms.author: riande ms.date: 11/30/2019 no-loc:'
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: e3b73a7a559d2f4a0803dc26dd42257c60fab884
ms.sourcegitcommit: e95b90585a9bc353f57643ed7d8e652dc498359b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84356961"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="1f305-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1f305-103">'Blazor'</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1f305-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1f305-104">'Identity'</span></span>

<span data-ttu-id="1f305-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1f305-105">'Let's Encrypt'</span></span> <span data-ttu-id="1f305-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1f305-106">'Razor'</span></span>

<span data-ttu-id="1f305-107">'SignalR' uid: fundamentals/localization</span><span class="sxs-lookup"><span data-stu-id="1f305-107">'SignalR' uid: fundamentals/localization</span></span> <span data-ttu-id="1f305-108">ASP.NET Core のグローバリゼーションおよびローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-108">Globalization and localization in ASP.NET Core</span></span> <span data-ttu-id="1f305-109">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="1f305-109">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="1f305-110">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-110">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="1f305-111">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-111">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="1f305-112">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-112">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="1f305-113">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-113">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="1f305-114">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-114">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="1f305-115">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-115">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="1f305-116">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-116">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="1f305-117">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="1f305-117">App localization involves the following:</span></span>

<span data-ttu-id="1f305-118">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-118">Make the app's content localizable</span></span> <span data-ttu-id="1f305-119">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-119">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-120">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-120">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-121">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1f305-121">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="1f305-122">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-122">Make the app's content localizable</span></span> <span data-ttu-id="1f305-123"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-123"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="1f305-124">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-124">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="1f305-125">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-125">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="1f305-126">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-126">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="1f305-127">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-127">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="1f305-128">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-128">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="1f305-129">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="1f305-129">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="1f305-130">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-130">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="1f305-131">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-131">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="1f305-132">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="1f305-132">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="1f305-133">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-133">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="1f305-134">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-134">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="1f305-135">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="1f305-135">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="1f305-136">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-136">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="1f305-137">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-137">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="1f305-138">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-138">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="1f305-139">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-139">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="1f305-140">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-140">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="1f305-141">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-141">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="1f305-142">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-142">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="1f305-143">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-143">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="1f305-144">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-144">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="1f305-145">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-145">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="1f305-146">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-146">View localization</span></span> <span data-ttu-id="1f305-147">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-147">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="1f305-148">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-148">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="1f305-149">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-149">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="1f305-150">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-150">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="1f305-151">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-151">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="1f305-152">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-152">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="1f305-153">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-153">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="1f305-154">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-154">Consider the following Razor markup:</span></span>

<span data-ttu-id="1f305-155">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-155">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="1f305-156">Key</span><span class="sxs-lookup"><span data-stu-id="1f305-156">Key</span></span>

<span data-ttu-id="1f305-157">[値]</span><span class="sxs-lookup"><span data-stu-id="1f305-157">Value</span></span> <span data-ttu-id="1f305-158">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-158">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="1f305-159">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-159">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="1f305-160">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1f305-160">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="1f305-161">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-161">DataAnnotations localization</span></span> <span data-ttu-id="1f305-162">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="1f305-162">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="1f305-163">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-163">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="1f305-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

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

<span data-ttu-id="1f305-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="1f305-166">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-166">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="1f305-167">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="1f305-167">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="1f305-168">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-168">Using one resource string for multiple classes</span></span>

<span data-ttu-id="1f305-169">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-169">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="1f305-170">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-170">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="1f305-171">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-171">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="1f305-172">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-172">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-173">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="1f305-173">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="1f305-174">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-174">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="1f305-175">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-175">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="1f305-176">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-176">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="1f305-177">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-177">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="1f305-178">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-178">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="1f305-179">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="1f305-179">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="1f305-180">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-180">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="1f305-181">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="1f305-181">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="1f305-182">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-182">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="1f305-183">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="1f305-183">Resource files</span></span>

1. <span data-ttu-id="1f305-184">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="1f305-184">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。](localization/_static/newi.png)

2. <span data-ttu-id="1f305-187">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-187">"es" is the language code for Spanish.</span></span>

    ![Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。](localization/_static/res.png)

3. <span data-ttu-id="1f305-189">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-189">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/hola.png)

    <span data-ttu-id="1f305-191">[追加] の 2 つ目のコンテキスト メニューが開き、[新しい項目] コマンドが強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-191">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="1f305-193">[新しい項目の追加] ダイアログ</span><span class="sxs-lookup"><span data-stu-id="1f305-193">Add New Item dialog</span></span>

<span data-ttu-id="1f305-194">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="1f305-194">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="1f305-195">Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)</span><span class="sxs-lookup"><span data-stu-id="1f305-195">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="1f305-196">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-196">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="1f305-197">Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー</span><span class="sxs-lookup"><span data-stu-id="1f305-197">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="1f305-198">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-198">Resource file naming</span></span>

<span data-ttu-id="1f305-199">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-199">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="1f305-200">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-200">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="1f305-201">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-201">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="1f305-202">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-202">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="1f305-203">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-203">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="1f305-204">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-204">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="1f305-205">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-205">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="1f305-206">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-206">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="1f305-207">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-207">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="1f305-208">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-208">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="1f305-209">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-209">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="1f305-210">リソース名</span><span class="sxs-lookup"><span data-stu-id="1f305-210">Resource name</span></span> |
|    |     |

<span data-ttu-id="1f305-211">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-211">Dot or path naming</span></span> <span data-ttu-id="1f305-212">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-212">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="1f305-213">ドット</span><span class="sxs-lookup"><span data-stu-id="1f305-213">Dot</span></span> <span data-ttu-id="1f305-214">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-214">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="1f305-215">パス</span><span class="sxs-lookup"><span data-stu-id="1f305-215">Path</span></span>

* <span data-ttu-id="1f305-216">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="1f305-216">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="1f305-217">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-217">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="1f305-218"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="1f305-218"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="1f305-219">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-219">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="1f305-220">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-220">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="1f305-221">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-221">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="1f305-222">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-222">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="1f305-223">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="1f305-223">RootNamespaceAttribute</span></span>
* <span data-ttu-id="1f305-224">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-224">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="1f305-225">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-225">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="1f305-226">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="1f305-226">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="1f305-227">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="1f305-227">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="1f305-228">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-228">Localization does not work by default.</span></span>

<span data-ttu-id="1f305-229">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="1f305-229">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="1f305-230">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="1f305-230">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="1f305-231">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="1f305-231">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="1f305-232">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-232">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="1f305-233">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="1f305-233">Culture fallback behavior</span></span> <span data-ttu-id="1f305-234">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="1f305-234">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="1f305-235">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1f305-235">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="1f305-236">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-236">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="1f305-237">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-237">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="1f305-238">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-238">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="1f305-239">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="1f305-239">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="1f305-240">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-240">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="1f305-241">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-241">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="1f305-242">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-242">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="1f305-243">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-243">*Welcome.fr.resx*</span></span>

<span data-ttu-id="1f305-244">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="1f305-244">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="1f305-245">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-245">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="1f305-246">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-246">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="1f305-247">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-247">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="1f305-248">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="1f305-248">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="1f305-249">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f305-249">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="1f305-250">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="1f305-250">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="1f305-251">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-251">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="1f305-252">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1f305-252">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="1f305-253">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-253">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="1f305-254">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="1f305-254">Add other cultures</span></span>

<span data-ttu-id="1f305-255">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-255">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="1f305-256">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-256">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="1f305-257">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-257">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="1f305-258">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-258">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-259">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="1f305-259">Configure localization</span></span> <span data-ttu-id="1f305-260">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-260">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="1f305-261">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-261">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="1f305-262">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-262">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="1f305-263">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-263">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="1f305-264">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="1f305-264">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="1f305-265">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-265">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1f305-266">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-266">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="1f305-267">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1f305-267">Localization middleware</span></span> <span data-ttu-id="1f305-268">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-268">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="1f305-269">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-269">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="1f305-270">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-270">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="1f305-271">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="1f305-271">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="1f305-272">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-272">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="1f305-273">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="1f305-273">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="1f305-274">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-274">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="1f305-275">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="1f305-275">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="1f305-276">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-276">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="1f305-277">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-277">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="1f305-278">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-278">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="1f305-279">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f305-279">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="1f305-280">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-280">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="1f305-281">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="1f305-281">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="1f305-282">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-282">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="1f305-283">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-283">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="1f305-284">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-284">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="1f305-285">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-285">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="1f305-286">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-286">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="1f305-287">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-287">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="1f305-288">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="1f305-288">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="1f305-289">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-289">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="1f305-290">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-290">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="1f305-291">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-291">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="1f305-292">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="1f305-292">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="1f305-293">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="1f305-293">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="1f305-294">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-294">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。](localization/_static/lang.png)

3. <span data-ttu-id="1f305-296">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-296">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="1f305-297">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-297">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="1f305-298">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-298">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="1f305-299">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-299">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="1f305-300">インターネット オプション</span><span class="sxs-lookup"><span data-stu-id="1f305-300">Internet Options</span></span>

<span data-ttu-id="1f305-301">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-301">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="1f305-302">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-302">Tap **Add a language**.</span></span> <span data-ttu-id="1f305-303">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-303">Add the language.</span></span>

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

<span data-ttu-id="1f305-304">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-304">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="1f305-305">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-305">Use a custom provider</span></span>

<span data-ttu-id="1f305-306">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-306">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="1f305-307">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-307">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="1f305-308">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-308">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="1f305-309">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-309">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="1f305-310">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-310">Set the culture programmatically</span></span> <span data-ttu-id="1f305-311">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f305-311">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="1f305-312">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-312">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="1f305-313">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-313">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="1f305-314">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-314">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="1f305-315">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-315">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="1f305-316">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-316">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="1f305-317">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="1f305-317">Model binding route data and query strings</span></span>

<span data-ttu-id="1f305-318">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-318">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="1f305-319">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="1f305-319">Globalization and localization terms</span></span> <span data-ttu-id="1f305-320">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-320">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="1f305-321">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-321">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="1f305-322">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="1f305-322">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="1f305-323">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-323">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="1f305-324">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-324">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="1f305-325">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-325">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="1f305-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="1f305-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="1f305-327">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-327">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="1f305-328">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-328">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="1f305-329">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="1f305-329">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="1f305-330">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="1f305-330">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="1f305-331">用語:</span><span class="sxs-lookup"><span data-stu-id="1f305-331">Terms:</span></span> <span data-ttu-id="1f305-332">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-332">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="1f305-333">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-333">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="1f305-334">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-334">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="1f305-335">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="1f305-335">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="1f305-336">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-336">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="1f305-337">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="1f305-337">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="1f305-338">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-338">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="1f305-339">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="1f305-339">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="1f305-340">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-340">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="1f305-341">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="1f305-341">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="1f305-342">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="1f305-342">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="1f305-343">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f305-343">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f305-344">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="1f305-344">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="1f305-345">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="1f305-345">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="1f305-346">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="1f305-346">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="1f305-347">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="1f305-347">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="1f305-348">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="1f305-348">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="1f305-349">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="1f305-349">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="1f305-350">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-350">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="1f305-351">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-351">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="1f305-352">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-352">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="1f305-353">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-353">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="1f305-354">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-354">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="1f305-355">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-355">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="1f305-356">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-356">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="1f305-357">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="1f305-357">App localization involves the following:</span></span>

<span data-ttu-id="1f305-358">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-358">Make the app's content localizable</span></span> <span data-ttu-id="1f305-359">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-359">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-360">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-360">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-361">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1f305-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="1f305-362">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-362">Make the app's content localizable</span></span> <span data-ttu-id="1f305-363"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-363"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="1f305-364">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-364">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="1f305-365">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-365">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="1f305-366">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-366">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="1f305-367">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-367">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="1f305-368">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-368">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="1f305-369">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="1f305-369">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="1f305-370">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-370">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="1f305-371">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-371">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="1f305-372">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="1f305-372">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="1f305-373">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-373">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="1f305-374">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-374">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="1f305-375">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="1f305-375">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="1f305-376">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-376">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="1f305-377">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-377">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="1f305-378">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-378">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="1f305-379">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-379">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="1f305-380">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-380">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="1f305-381">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-381">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="1f305-382">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-382">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="1f305-383">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-383">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="1f305-384">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-384">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="1f305-385">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-385">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="1f305-386">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-386">View localization</span></span> <span data-ttu-id="1f305-387">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-387">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="1f305-388">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-388">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="1f305-389">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-389">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="1f305-390">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-390">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="1f305-391">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-391">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="1f305-392">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-392">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="1f305-393">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-393">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="1f305-394">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-394">Consider the following Razor markup:</span></span>

<span data-ttu-id="1f305-395">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-395">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="1f305-396">Key</span><span class="sxs-lookup"><span data-stu-id="1f305-396">Key</span></span>

<span data-ttu-id="1f305-397">[値]</span><span class="sxs-lookup"><span data-stu-id="1f305-397">Value</span></span> <span data-ttu-id="1f305-398">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-398">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="1f305-399">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-399">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="1f305-400">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1f305-400">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="1f305-401">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-401">DataAnnotations localization</span></span> <span data-ttu-id="1f305-402">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="1f305-402">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="1f305-403">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-403">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="1f305-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

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

<span data-ttu-id="1f305-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="1f305-406">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-406">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="1f305-407">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="1f305-407">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="1f305-408">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-408">Using one resource string for multiple classes</span></span>

<span data-ttu-id="1f305-409">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-409">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="1f305-410">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-410">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="1f305-411">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-411">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="1f305-412">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-412">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-413">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="1f305-413">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="1f305-414">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-414">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="1f305-415">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-415">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="1f305-416">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-416">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="1f305-417">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-417">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="1f305-418">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-418">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="1f305-419">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="1f305-419">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="1f305-420">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-420">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="1f305-421">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="1f305-421">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="1f305-422">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-422">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="1f305-423">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="1f305-423">Resource files</span></span>

1. <span data-ttu-id="1f305-424">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="1f305-424">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。](localization/_static/newi.png)

2. <span data-ttu-id="1f305-427">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-427">"es" is the language code for Spanish.</span></span>

    ![Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。](localization/_static/res.png)

3. <span data-ttu-id="1f305-429">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-429">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/hola.png)

    <span data-ttu-id="1f305-431">[追加] の 2 つ目のコンテキスト メニューが開き、[新しい項目] コマンドが強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-431">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="1f305-433">[新しい項目の追加] ダイアログ</span><span class="sxs-lookup"><span data-stu-id="1f305-433">Add New Item dialog</span></span>

<span data-ttu-id="1f305-434">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="1f305-434">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="1f305-435">Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)</span><span class="sxs-lookup"><span data-stu-id="1f305-435">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="1f305-436">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-436">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="1f305-437">Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー</span><span class="sxs-lookup"><span data-stu-id="1f305-437">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="1f305-438">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-438">Resource file naming</span></span>

<span data-ttu-id="1f305-439">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-439">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="1f305-440">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-440">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="1f305-441">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-441">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="1f305-442">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-442">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="1f305-443">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-443">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="1f305-444">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-444">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="1f305-445">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-445">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="1f305-446">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-446">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="1f305-447">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-447">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="1f305-448">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-448">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="1f305-449">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-449">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="1f305-450">リソース名</span><span class="sxs-lookup"><span data-stu-id="1f305-450">Resource name</span></span> |
|    |     |

<span data-ttu-id="1f305-451">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-451">Dot or path naming</span></span> <span data-ttu-id="1f305-452">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-452">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="1f305-453">ドット</span><span class="sxs-lookup"><span data-stu-id="1f305-453">Dot</span></span> <span data-ttu-id="1f305-454">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-454">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="1f305-455">パス</span><span class="sxs-lookup"><span data-stu-id="1f305-455">Path</span></span>

* <span data-ttu-id="1f305-456">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="1f305-456">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="1f305-457">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-457">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="1f305-458"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="1f305-458"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="1f305-459">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-459">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="1f305-460">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-460">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="1f305-461">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-461">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="1f305-462">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-462">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="1f305-463">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="1f305-463">RootNamespaceAttribute</span></span>
* <span data-ttu-id="1f305-464">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-464">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="1f305-465">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-465">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="1f305-466">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="1f305-466">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="1f305-467">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="1f305-467">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="1f305-468">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-468">Localization does not work by default.</span></span>

<span data-ttu-id="1f305-469">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="1f305-469">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="1f305-470">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="1f305-470">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="1f305-471">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="1f305-471">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="1f305-472">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-472">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="1f305-473">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="1f305-473">Culture fallback behavior</span></span> <span data-ttu-id="1f305-474">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="1f305-474">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="1f305-475">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1f305-475">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="1f305-476">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-476">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="1f305-477">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-477">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="1f305-478">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-478">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="1f305-479">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="1f305-479">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="1f305-480">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-480">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="1f305-481">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-481">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="1f305-482">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-482">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="1f305-483">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-483">*Welcome.fr.resx*</span></span>

<span data-ttu-id="1f305-484">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="1f305-484">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="1f305-485">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-485">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="1f305-486">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-486">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="1f305-487">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-487">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="1f305-488">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="1f305-488">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="1f305-489">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f305-489">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="1f305-490">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="1f305-490">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="1f305-491">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-491">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="1f305-492">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1f305-492">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="1f305-493">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-493">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="1f305-494">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="1f305-494">Add other cultures</span></span>

<span data-ttu-id="1f305-495">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-495">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="1f305-496">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-496">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="1f305-497">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-497">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="1f305-498">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-498">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-499">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="1f305-499">Configure localization</span></span> <span data-ttu-id="1f305-500">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-500">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="1f305-501">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-501">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="1f305-502">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-502">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="1f305-503">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-503">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="1f305-504">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="1f305-504">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="1f305-505">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-505">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1f305-506">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-506">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="1f305-507">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1f305-507">Localization middleware</span></span> <span data-ttu-id="1f305-508">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-508">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="1f305-509">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-509">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="1f305-510">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-510">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="1f305-511">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="1f305-511">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="1f305-512">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-512">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="1f305-513">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="1f305-513">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="1f305-514">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-514">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="1f305-515">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="1f305-515">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="1f305-516">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-516">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="1f305-517">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-517">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="1f305-518">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-518">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="1f305-519">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f305-519">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="1f305-520">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-520">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="1f305-521">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="1f305-521">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="1f305-522">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-522">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="1f305-523">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-523">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="1f305-524">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-524">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="1f305-525">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-525">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="1f305-526">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-526">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="1f305-527">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-527">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="1f305-528">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="1f305-528">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="1f305-529">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-529">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="1f305-530">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-530">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="1f305-531">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-531">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="1f305-532">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="1f305-532">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="1f305-533">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="1f305-533">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="1f305-534">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-534">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。](localization/_static/lang.png)

3. <span data-ttu-id="1f305-536">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-536">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="1f305-537">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-537">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="1f305-538">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-538">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="1f305-539">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-539">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="1f305-540">インターネット オプション</span><span class="sxs-lookup"><span data-stu-id="1f305-540">Internet Options</span></span>

<span data-ttu-id="1f305-541">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-541">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="1f305-542">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-542">Tap **Add a language**.</span></span> <span data-ttu-id="1f305-543">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-543">Add the language.</span></span>

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

<span data-ttu-id="1f305-544">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-544">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="1f305-545">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-545">Use a custom provider</span></span>

<span data-ttu-id="1f305-546">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-546">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="1f305-547">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-547">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="1f305-548">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-548">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="1f305-549">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-549">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="1f305-550">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-550">Set the culture programmatically</span></span> <span data-ttu-id="1f305-551">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f305-551">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="1f305-552">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-552">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="1f305-553">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-553">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="1f305-554">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-554">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="1f305-555">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-555">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="1f305-556">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-556">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="1f305-557">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="1f305-557">Model binding route data and query strings</span></span>

<span data-ttu-id="1f305-558">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-558">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="1f305-559">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="1f305-559">Globalization and localization terms</span></span> <span data-ttu-id="1f305-560">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-560">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="1f305-561">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-561">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="1f305-562">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="1f305-562">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="1f305-563">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-563">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="1f305-564">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-564">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="1f305-565">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-565">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="1f305-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="1f305-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="1f305-567">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-567">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="1f305-568">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-568">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="1f305-569">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="1f305-569">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="1f305-570">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="1f305-570">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="1f305-571">用語:</span><span class="sxs-lookup"><span data-stu-id="1f305-571">Terms:</span></span> <span data-ttu-id="1f305-572">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-572">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="1f305-573">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-573">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="1f305-574">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-574">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="1f305-575">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="1f305-575">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="1f305-576">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-576">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="1f305-577">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="1f305-577">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="1f305-578">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-578">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="1f305-579">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="1f305-579">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="1f305-580">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-580">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="1f305-581">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="1f305-581">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="1f305-582">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="1f305-582">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="1f305-583">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f305-583">Additional resources</span></span>

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="1f305-584">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="1f305-584">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="1f305-585">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="1f305-585">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="1f305-586">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="1f305-586">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="1f305-587">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="1f305-587">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="1f305-588">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="1f305-588">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="1f305-589">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="1f305-589">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="1f305-590">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-590">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="1f305-591">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-591">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="1f305-592">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-592">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="1f305-593">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-593">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="1f305-594">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-594">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="1f305-595">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-595">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="1f305-596">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-596">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="1f305-597">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="1f305-597">App localization involves the following:</span></span>

<span data-ttu-id="1f305-598">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-598">Make the app's content localizable</span></span> <span data-ttu-id="1f305-599">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-599">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-600">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-600">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-601">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1f305-601">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="1f305-602">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="1f305-602">Make the app's content localizable</span></span> <span data-ttu-id="1f305-603"><xref:Microsoft.Extensions.Localization.IStringLocalizer> と <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> は、ローカライズされたアプリの開発時に生産性が向上するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-603"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="1f305-604">`IStringLocalizer` では、<xref:System.Resources.ResourceManager> と <xref:System.Resources.ResourceReader> を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-604">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="1f305-605">インターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-605">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="1f305-606">`IStringLocalizer` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-606">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="1f305-607">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-607">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="1f305-608">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-608">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="1f305-609">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="1f305-609">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="1f305-610">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-610">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="1f305-611">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-611">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="1f305-612">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="1f305-612">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="1f305-613">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-613">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="1f305-614">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-614">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="1f305-615">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="1f305-615">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="1f305-616">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-616">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="1f305-617">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-617">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="1f305-618">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-618">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="1f305-619">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-619">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="1f305-620">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-620">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="1f305-621">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-621">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="1f305-622">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-622">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="1f305-623">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-623">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="1f305-624">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-624">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="1f305-625">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1f305-625">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="1f305-626">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-626">View localization</span></span> <span data-ttu-id="1f305-627">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-627">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="1f305-628">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-628">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="1f305-629">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-629">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="1f305-630">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="1f305-630">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="1f305-631">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="1f305-631">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="1f305-632">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-632">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="1f305-633">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="1f305-633">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="1f305-634">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-634">Consider the following Razor markup:</span></span>

<span data-ttu-id="1f305-635">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-635">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="1f305-636">Key</span><span class="sxs-lookup"><span data-stu-id="1f305-636">Key</span></span>

<span data-ttu-id="1f305-637">[値]</span><span class="sxs-lookup"><span data-stu-id="1f305-637">Value</span></span> <span data-ttu-id="1f305-638">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-638">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="1f305-639">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-639">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="1f305-640">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1f305-640">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="1f305-641">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="1f305-641">DataAnnotations localization</span></span> <span data-ttu-id="1f305-642">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="1f305-642">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="1f305-643">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-643">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="1f305-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

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

<span data-ttu-id="1f305-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="1f305-646">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-646">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="1f305-647">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="1f305-647">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="1f305-648">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-648">Using one resource string for multiple classes</span></span>

<span data-ttu-id="1f305-649">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-649">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="1f305-650">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-650">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="1f305-651">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-651">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="1f305-652">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="1f305-652">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="1f305-653">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="1f305-653">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="1f305-654">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-654">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="1f305-655">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-655">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="1f305-656">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-656">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="1f305-657">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-657">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="1f305-658">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-658">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="1f305-659">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="1f305-659">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="1f305-660">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-660">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="1f305-661">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="1f305-661">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="1f305-662">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-662">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="1f305-663">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="1f305-663">Resource files</span></span>

1. <span data-ttu-id="1f305-664">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="1f305-664">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。](localization/_static/newi.png)

2. <span data-ttu-id="1f305-667">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-667">"es" is the language code for Spanish.</span></span>

    ![Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。](localization/_static/res.png)

3. <span data-ttu-id="1f305-669">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-669">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/hola.png)

    <span data-ttu-id="1f305-671">[追加] の 2 つ目のコンテキスト メニューが開き、[新しい項目] コマンドが強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-671">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="1f305-673">[新しい項目の追加] ダイアログ</span><span class="sxs-lookup"><span data-stu-id="1f305-673">Add New Item dialog</span></span>

<span data-ttu-id="1f305-674">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="1f305-674">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="1f305-675">Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)</span><span class="sxs-lookup"><span data-stu-id="1f305-675">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="1f305-676">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-676">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="1f305-677">Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー</span><span class="sxs-lookup"><span data-stu-id="1f305-677">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="1f305-678">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-678">Resource file naming</span></span>

<span data-ttu-id="1f305-679">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-679">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="1f305-680">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-680">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="1f305-681">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-681">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="1f305-682">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-682">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="1f305-683">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-683">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="1f305-684">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-684">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="1f305-685">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-685">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="1f305-686">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="1f305-686">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="1f305-687">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-687">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="1f305-688">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-688">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="1f305-689">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="1f305-689">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="1f305-690">リソース名</span><span class="sxs-lookup"><span data-stu-id="1f305-690">Resource name</span></span> |
|    |     |

<span data-ttu-id="1f305-691">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="1f305-691">Dot or path naming</span></span> <span data-ttu-id="1f305-692">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-692">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="1f305-693">ドット</span><span class="sxs-lookup"><span data-stu-id="1f305-693">Dot</span></span> <span data-ttu-id="1f305-694">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-694">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="1f305-695">パス</span><span class="sxs-lookup"><span data-stu-id="1f305-695">Path</span></span>

* <span data-ttu-id="1f305-696">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="1f305-696">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="1f305-697">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-697">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="1f305-698"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="1f305-698"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="1f305-699">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-699">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="1f305-700">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-700">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="1f305-701">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="1f305-701">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="1f305-702">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-702">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="1f305-703">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="1f305-703">RootNamespaceAttribute</span></span>
* <span data-ttu-id="1f305-704">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-704">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="1f305-705">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-705">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="1f305-706">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="1f305-706">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="1f305-707">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="1f305-707">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="1f305-708">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-708">Localization does not work by default.</span></span>

<span data-ttu-id="1f305-709">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="1f305-709">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="1f305-710">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="1f305-710">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="1f305-711">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="1f305-711">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="1f305-712">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-712">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="1f305-713">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="1f305-713">Culture fallback behavior</span></span> <span data-ttu-id="1f305-714">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="1f305-714">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="1f305-715">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1f305-715">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="1f305-716">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-716">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="1f305-717">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-717">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="1f305-718">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-718">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="1f305-719">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="1f305-719">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="1f305-720">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-720">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="1f305-721">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f305-721">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="1f305-722">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-722">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="1f305-723">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="1f305-723">*Welcome.fr.resx*</span></span>

<span data-ttu-id="1f305-724">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="1f305-724">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="1f305-725">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="1f305-725">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="1f305-726">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-726">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="1f305-727">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-727">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="1f305-728">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="1f305-728">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="1f305-729">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f305-729">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="1f305-730">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="1f305-730">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="1f305-731">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-731">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="1f305-732">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1f305-732">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="1f305-733">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="1f305-733">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="1f305-734">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="1f305-734">Add other cultures</span></span>

<span data-ttu-id="1f305-735">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-735">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="1f305-736">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-736">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="1f305-737">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="1f305-737">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="1f305-738">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="1f305-738">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="1f305-739">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="1f305-739">Configure localization</span></span> <span data-ttu-id="1f305-740">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-740">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="1f305-741">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-741">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="1f305-742">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-742">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="1f305-743">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-743">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="1f305-744">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="1f305-744">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="1f305-745">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="1f305-745">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1f305-746">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-746">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="1f305-747">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="1f305-747">Localization middleware</span></span> <span data-ttu-id="1f305-748">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-748">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="1f305-749">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-749">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="1f305-750">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-750">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="1f305-751">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="1f305-751">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="1f305-752">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-752">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="1f305-753">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="1f305-753">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="1f305-754">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="1f305-754">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="1f305-755">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="1f305-755">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="1f305-756">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-756">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="1f305-757">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-757">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="1f305-758">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-758">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="1f305-759">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f305-759">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="1f305-760">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-760">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="1f305-761">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="1f305-761">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="1f305-762">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-762">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="1f305-763">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-763">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="1f305-764">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-764">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="1f305-765">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="1f305-765">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="1f305-766">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="1f305-766">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="1f305-767">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-767">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="1f305-768">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="1f305-768">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="1f305-769">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-769">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="1f305-770">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-770">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="1f305-771">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-771">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="1f305-772">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="1f305-772">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="1f305-773">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="1f305-773">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="1f305-774">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-774">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。](localization/_static/lang.png)

3. <span data-ttu-id="1f305-776">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f305-776">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="1f305-777">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-777">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="1f305-778">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-778">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="1f305-779">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-779">Tap **Languages**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="1f305-780">インターネット オプション</span><span class="sxs-lookup"><span data-stu-id="1f305-780">Internet Options</span></span>

<span data-ttu-id="1f305-781">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-781">Tap **Set Language Preferences**.</span></span>

 - <span data-ttu-id="1f305-782">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-782">Tap **Add a language**.</span></span>
 - <span data-ttu-id="1f305-783">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="1f305-783">Add the language.</span></span>

<span data-ttu-id="1f305-784">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="1f305-784">Tap the language, then tap **Move Up**.</span></span>

<span data-ttu-id="1f305-785">Content-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="1f305-785">The Content-Language HTTP header</span></span>

<span data-ttu-id="1f305-786">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) エンティティ ヘッダーは、</span><span class="sxs-lookup"><span data-stu-id="1f305-786">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="1f305-787">対象ユーザー用に想定した言語を説明するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-787">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="1f305-788">ユーザーが、そのユーザー独自の優先言語に従って区別することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="1f305-788">Allows a user to differentiate according to the users' own preferred language.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="1f305-789">エンティティ ヘッダーは、HTTP 要求と応答の両方で使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-789">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="1f305-790">プロパティ `ApplyCurrentCultureToResponseHeaders` を設定することによって `Content-Language` ヘッダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-790">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span> <span data-ttu-id="1f305-791">`Content-Language` ヘッダーを追加すると、</span><span class="sxs-lookup"><span data-stu-id="1f305-791">Adding the `Content-Language` header:</span></span> <span data-ttu-id="1f305-792">RequestLocalizationMiddleware で `CurrentUICulture` を使って `Content-Language` を設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="1f305-792">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>

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

<span data-ttu-id="1f305-793">応答ヘッダーの `Content-Language` を明示的に設定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="1f305-793">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="1f305-794">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="1f305-794">Use a custom provider</span></span>

<span data-ttu-id="1f305-795">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="1f305-795">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="1f305-796">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f305-796">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="1f305-797">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f305-797">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="1f305-798">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1f305-798">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="1f305-799">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="1f305-799">Set the culture programmatically</span></span> <span data-ttu-id="1f305-800">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f305-800">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="1f305-801">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="1f305-801">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="1f305-802">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f305-802">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="1f305-803">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f305-803">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="1f305-804">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f305-804">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="1f305-805">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="1f305-805">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="1f305-806">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="1f305-806">Model binding route data and query strings</span></span>

<span data-ttu-id="1f305-807">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-807">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="1f305-808">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="1f305-808">Globalization and localization terms</span></span> <span data-ttu-id="1f305-809">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="1f305-809">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="1f305-810">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="1f305-810">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="1f305-811">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="1f305-811">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="1f305-812">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="1f305-812">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="1f305-813">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="1f305-813">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="1f305-814">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="1f305-814">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="1f305-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="1f305-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="1f305-816">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f305-816">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="1f305-817">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="1f305-817">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="1f305-818">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="1f305-818">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="1f305-819">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="1f305-819">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="1f305-820">用語:</span><span class="sxs-lookup"><span data-stu-id="1f305-820">Terms:</span></span> <span data-ttu-id="1f305-821">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-821">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="1f305-822">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="1f305-822">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="1f305-823">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="1f305-823">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="1f305-824">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="1f305-824">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="1f305-825">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-825">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="1f305-826">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="1f305-826">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="1f305-827">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-827">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="1f305-828">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="1f305-828">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="1f305-829">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="1f305-829">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="1f305-830">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="1f305-830">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="1f305-831">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="1f305-831">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="1f305-832">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f305-832">Additional resources</span></span>

::: moniker-end
