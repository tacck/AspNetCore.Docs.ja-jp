---
<span data-ttu-id="acd0c-101">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-102">'Blazor'</span></span>
- <span data-ttu-id="acd0c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-103">'Identity'</span></span>
- <span data-ttu-id="acd0c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-105">'Razor'</span></span>
- <span data-ttu-id="acd0c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="acd0c-107">ASP.NET Core のグローバリゼーションおよびローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="acd0c-108">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acd0c-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acd0c-109">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acd0c-110">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acd0c-111">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acd0c-112">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acd0c-113">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acd0c-114">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acd0c-115">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acd0c-116">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-116">App localization involves the following:</span></span>

1. <span data-ttu-id="acd0c-117">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="acd0c-118">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acd0c-119">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acd0c-120">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="acd0c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acd0c-121">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-121">Make the app's content localizable</span></span>

<span data-ttu-id="acd0c-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acd0c-123">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acd0c-124">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acd0c-125">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acd0c-126">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acd0c-127">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acd0c-128">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acd0c-129">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acd0c-130">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acd0c-131">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acd0c-132">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acd0c-133">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acd0c-134">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acd0c-135">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-136">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acd0c-137">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acd0c-138">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acd0c-139">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acd0c-140">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acd0c-141">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acd0c-142">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-142">View localization</span></span>

<span data-ttu-id="acd0c-143">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acd0c-144">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acd0c-145">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acd0c-146">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acd0c-147">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acd0c-148">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acd0c-149">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acd0c-150">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acd0c-151">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acd0c-152">Key</span><span class="sxs-lookup"><span data-stu-id="acd0c-152">Key</span></span> | <span data-ttu-id="acd0c-153">[値]</span><span class="sxs-lookup"><span data-stu-id="acd0c-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="acd0c-154">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-155">'Blazor'</span></span>
- <span data-ttu-id="acd0c-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-156">'Identity'</span></span>
- <span data-ttu-id="acd0c-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-158">'Razor'</span></span>
- <span data-ttu-id="acd0c-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-159">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acd0c-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acd0c-161">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acd0c-162">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-163">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acd0c-164">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-164">DataAnnotations localization</span></span>

<span data-ttu-id="acd0c-165">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acd0c-166">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acd0c-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acd0c-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acd0c-169">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acd0c-170">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="acd0c-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acd0c-171">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acd0c-172">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="acd0c-173">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acd0c-174">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acd0c-175">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acd0c-176">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acd0c-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acd0c-177">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acd0c-178">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acd0c-179">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acd0c-180">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acd0c-181">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acd0c-182">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acd0c-183">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acd0c-184">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acd0c-185">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acd0c-186">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="acd0c-186">Resource files</span></span>

<span data-ttu-id="acd0c-187">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acd0c-188">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acd0c-189">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acd0c-190">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acd0c-191">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acd0c-192">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

2. <span data-ttu-id="acd0c-195">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

3. <span data-ttu-id="acd0c-197">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

    <span data-ttu-id="acd0c-199">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acd0c-201">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-201">Resource file naming</span></span>

<span data-ttu-id="acd0c-202">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acd0c-203">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acd0c-204">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-205">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acd0c-206">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acd0c-207">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-208">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acd0c-209">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-210">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acd0c-211">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-212">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acd0c-213">リソース名</span><span class="sxs-lookup"><span data-stu-id="acd0c-213">Resource name</span></span> | <span data-ttu-id="acd0c-214">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acd0c-215">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-216">'Blazor'</span></span>
- <span data-ttu-id="acd0c-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-217">'Identity'</span></span>
- <span data-ttu-id="acd0c-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-219">'Razor'</span></span>
- <span data-ttu-id="acd0c-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-221">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-222">'Blazor'</span></span>
- <span data-ttu-id="acd0c-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-223">'Identity'</span></span>
- <span data-ttu-id="acd0c-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-225">'Razor'</span></span>
- <span data-ttu-id="acd0c-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-227">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-228">'Blazor'</span></span>
- <span data-ttu-id="acd0c-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-229">'Identity'</span></span>
- <span data-ttu-id="acd0c-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-231">'Razor'</span></span>
- <span data-ttu-id="acd0c-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-233">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-234">'Blazor'</span></span>
- <span data-ttu-id="acd0c-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-235">'Identity'</span></span>
- <span data-ttu-id="acd0c-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-237">'Razor'</span></span>
- <span data-ttu-id="acd0c-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-238">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-240">'Blazor'</span></span>
- <span data-ttu-id="acd0c-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-241">'Identity'</span></span>
- <span data-ttu-id="acd0c-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-243">'Razor'</span></span>
- <span data-ttu-id="acd0c-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-245">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-246">'Blazor'</span></span>
- <span data-ttu-id="acd0c-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-247">'Identity'</span></span>
- <span data-ttu-id="acd0c-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-249">'Razor'</span></span>
- <span data-ttu-id="acd0c-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-251">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-252">'Blazor'</span></span>
- <span data-ttu-id="acd0c-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-253">'Identity'</span></span>
- <span data-ttu-id="acd0c-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-255">'Razor'</span></span>
- <span data-ttu-id="acd0c-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-257">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-258">'Blazor'</span></span>
- <span data-ttu-id="acd0c-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-259">'Identity'</span></span>
- <span data-ttu-id="acd0c-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-261">'Razor'</span></span>
- <span data-ttu-id="acd0c-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-262">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-263">------- | | Resources/Controllers.HomeController.fr.resx | ドット  | | Resources/Controllers/HomeController.fr.resx  | パス | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acd0c-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acd0c-264">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acd0c-265">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acd0c-266"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acd0c-267">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acd0c-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acd0c-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acd0c-270">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acd0c-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acd0c-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acd0c-272">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acd0c-273">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acd0c-274">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acd0c-275">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="acd0c-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acd0c-276">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-276">Localization does not work by default.</span></span>
* <span data-ttu-id="acd0c-277">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acd0c-278">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acd0c-279">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acd0c-280">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acd0c-281">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="acd0c-281">Culture fallback behavior</span></span>

<span data-ttu-id="acd0c-282">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acd0c-283">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acd0c-284">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acd0c-285">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acd0c-286">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acd0c-287">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acd0c-288">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acd0c-289">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acd0c-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acd0c-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acd0c-292">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="acd0c-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acd0c-293">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acd0c-294">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acd0c-295">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acd0c-296">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acd0c-297">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acd0c-298">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acd0c-299">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acd0c-300">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acd0c-301">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acd0c-302">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="acd0c-302">Add other cultures</span></span>

<span data-ttu-id="acd0c-303">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acd0c-304">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acd0c-305">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acd0c-306">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acd0c-307">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-307">Configure localization</span></span>

<span data-ttu-id="acd0c-308">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acd0c-309">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acd0c-310">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acd0c-311">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acd0c-312">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acd0c-313">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acd0c-314">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acd0c-315">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="acd0c-315">Localization middleware</span></span>

<span data-ttu-id="acd0c-316">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acd0c-317">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acd0c-318">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acd0c-319">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acd0c-320">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acd0c-321">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acd0c-322">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acd0c-323">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acd0c-324">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acd0c-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-326">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acd0c-327">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acd0c-328">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acd0c-329">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acd0c-330">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acd0c-331">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acd0c-332">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acd0c-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-334">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acd0c-335">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acd0c-336">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acd0c-337">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acd0c-338">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acd0c-339">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acd0c-340">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="acd0c-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acd0c-341">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="acd0c-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acd0c-342">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acd0c-343">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acd0c-344">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acd0c-345">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acd0c-346">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acd0c-347">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-347">Tap **Languages**.</span></span>

    ![インターネット オプション](localization/_static/lang.png)

3. <span data-ttu-id="acd0c-349">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acd0c-350">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acd0c-351">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-351">Add the language.</span></span>

6. <span data-ttu-id="acd0c-352">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="acd0c-353">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-353">Use a custom provider</span></span>

<span data-ttu-id="acd0c-354">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acd0c-355">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acd0c-356">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="acd0c-357">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acd0c-358">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-358">Set the culture programmatically</span></span>

<span data-ttu-id="acd0c-359">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acd0c-360">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acd0c-361">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acd0c-362">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acd0c-363">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acd0c-364">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acd0c-365">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="acd0c-365">Model binding route data and query strings</span></span>

<span data-ttu-id="acd0c-366">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acd0c-367">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="acd0c-367">Globalization and localization terms</span></span>

<span data-ttu-id="acd0c-368">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acd0c-369">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acd0c-370">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acd0c-371">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acd0c-372">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acd0c-373">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acd0c-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acd0c-375">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acd0c-376">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acd0c-377">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acd0c-378">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acd0c-379">用語:</span><span class="sxs-lookup"><span data-stu-id="acd0c-379">Terms:</span></span>

* <span data-ttu-id="acd0c-380">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acd0c-381">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acd0c-382">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acd0c-383">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acd0c-384">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acd0c-385">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-385">(for example "en", "es")</span></span>
* <span data-ttu-id="acd0c-386">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acd0c-387">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acd0c-388">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acd0c-389">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acd0c-390">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="acd0c-391">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="acd0c-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acd0c-392">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acd0c-393">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="acd0c-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acd0c-394">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="acd0c-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acd0c-395">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="acd0c-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acd0c-396">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="acd0c-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="acd0c-397">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acd0c-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acd0c-398">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acd0c-399">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acd0c-400">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acd0c-401">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acd0c-402">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acd0c-403">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acd0c-404">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acd0c-405">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-405">App localization involves the following:</span></span>

1. <span data-ttu-id="acd0c-406">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="acd0c-407">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acd0c-408">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acd0c-409">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="acd0c-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acd0c-410">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-410">Make the app's content localizable</span></span>

<span data-ttu-id="acd0c-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acd0c-412">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acd0c-413">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acd0c-414">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acd0c-415">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acd0c-416">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acd0c-417">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acd0c-418">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acd0c-419">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acd0c-420">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acd0c-421">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acd0c-422">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acd0c-423">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acd0c-424">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-425">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acd0c-426">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acd0c-427">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acd0c-428">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acd0c-429">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acd0c-430">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acd0c-431">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-431">View localization</span></span>

<span data-ttu-id="acd0c-432">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acd0c-433">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acd0c-434">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acd0c-435">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acd0c-436">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acd0c-437">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acd0c-438">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acd0c-439">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acd0c-440">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acd0c-441">Key</span><span class="sxs-lookup"><span data-stu-id="acd0c-441">Key</span></span> | <span data-ttu-id="acd0c-442">[値]</span><span class="sxs-lookup"><span data-stu-id="acd0c-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="acd0c-443">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-444">'Blazor'</span></span>
- <span data-ttu-id="acd0c-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-445">'Identity'</span></span>
- <span data-ttu-id="acd0c-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-447">'Razor'</span></span>
- <span data-ttu-id="acd0c-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-448">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acd0c-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acd0c-450">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acd0c-451">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-452">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acd0c-453">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-453">DataAnnotations localization</span></span>

<span data-ttu-id="acd0c-454">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acd0c-455">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acd0c-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acd0c-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acd0c-458">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acd0c-459">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="acd0c-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acd0c-460">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acd0c-461">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="acd0c-462">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acd0c-463">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acd0c-464">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acd0c-465">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acd0c-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acd0c-466">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acd0c-467">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acd0c-468">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acd0c-469">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acd0c-470">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acd0c-471">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acd0c-472">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acd0c-473">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acd0c-474">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acd0c-475">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="acd0c-475">Resource files</span></span>

<span data-ttu-id="acd0c-476">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acd0c-477">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acd0c-478">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acd0c-479">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acd0c-480">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acd0c-481">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

2. <span data-ttu-id="acd0c-484">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

3. <span data-ttu-id="acd0c-486">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

    <span data-ttu-id="acd0c-488">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acd0c-490">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-490">Resource file naming</span></span>

<span data-ttu-id="acd0c-491">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acd0c-492">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acd0c-493">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-494">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acd0c-495">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acd0c-496">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-497">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acd0c-498">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-499">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acd0c-500">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-501">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acd0c-502">リソース名</span><span class="sxs-lookup"><span data-stu-id="acd0c-502">Resource name</span></span> | <span data-ttu-id="acd0c-503">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acd0c-504">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-505">'Blazor'</span></span>
- <span data-ttu-id="acd0c-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-506">'Identity'</span></span>
- <span data-ttu-id="acd0c-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-508">'Razor'</span></span>
- <span data-ttu-id="acd0c-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-510">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-511">'Blazor'</span></span>
- <span data-ttu-id="acd0c-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-512">'Identity'</span></span>
- <span data-ttu-id="acd0c-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-514">'Razor'</span></span>
- <span data-ttu-id="acd0c-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-516">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-517">'Blazor'</span></span>
- <span data-ttu-id="acd0c-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-518">'Identity'</span></span>
- <span data-ttu-id="acd0c-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-520">'Razor'</span></span>
- <span data-ttu-id="acd0c-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-522">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-523">'Blazor'</span></span>
- <span data-ttu-id="acd0c-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-524">'Identity'</span></span>
- <span data-ttu-id="acd0c-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-526">'Razor'</span></span>
- <span data-ttu-id="acd0c-527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-527">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-529">'Blazor'</span></span>
- <span data-ttu-id="acd0c-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-530">'Identity'</span></span>
- <span data-ttu-id="acd0c-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-532">'Razor'</span></span>
- <span data-ttu-id="acd0c-533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-534">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-535">'Blazor'</span></span>
- <span data-ttu-id="acd0c-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-536">'Identity'</span></span>
- <span data-ttu-id="acd0c-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-538">'Razor'</span></span>
- <span data-ttu-id="acd0c-539">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-540">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-541">'Blazor'</span></span>
- <span data-ttu-id="acd0c-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-542">'Identity'</span></span>
- <span data-ttu-id="acd0c-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-544">'Razor'</span></span>
- <span data-ttu-id="acd0c-545">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-546">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-547">'Blazor'</span></span>
- <span data-ttu-id="acd0c-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-548">'Identity'</span></span>
- <span data-ttu-id="acd0c-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-550">'Razor'</span></span>
- <span data-ttu-id="acd0c-551">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-551">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-552">------- | | Resources/Controllers.HomeController.fr.resx | ドット  | | Resources/Controllers/HomeController.fr.resx  | パス | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acd0c-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acd0c-553">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acd0c-554">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acd0c-555"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acd0c-556">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acd0c-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acd0c-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acd0c-559">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acd0c-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acd0c-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acd0c-561">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acd0c-562">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acd0c-563">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acd0c-564">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="acd0c-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acd0c-565">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-565">Localization does not work by default.</span></span>
* <span data-ttu-id="acd0c-566">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acd0c-567">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acd0c-568">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acd0c-569">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acd0c-570">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="acd0c-570">Culture fallback behavior</span></span>

<span data-ttu-id="acd0c-571">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acd0c-572">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acd0c-573">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acd0c-574">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acd0c-575">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acd0c-576">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acd0c-577">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acd0c-578">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acd0c-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acd0c-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acd0c-581">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="acd0c-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acd0c-582">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acd0c-583">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acd0c-584">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acd0c-585">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acd0c-586">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acd0c-587">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acd0c-588">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acd0c-589">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acd0c-590">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acd0c-591">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="acd0c-591">Add other cultures</span></span>

<span data-ttu-id="acd0c-592">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acd0c-593">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acd0c-594">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acd0c-595">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acd0c-596">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-596">Configure localization</span></span>

<span data-ttu-id="acd0c-597">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acd0c-598">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acd0c-599">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acd0c-600">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acd0c-601">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acd0c-602">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acd0c-603">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acd0c-604">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="acd0c-604">Localization middleware</span></span>

<span data-ttu-id="acd0c-605">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acd0c-606">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acd0c-607">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acd0c-608">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acd0c-609">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acd0c-610">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acd0c-611">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acd0c-612">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acd0c-613">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acd0c-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-615">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acd0c-616">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acd0c-617">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acd0c-618">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acd0c-619">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acd0c-620">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acd0c-621">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acd0c-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-623">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acd0c-624">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acd0c-625">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acd0c-626">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acd0c-627">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acd0c-628">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acd0c-629">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="acd0c-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acd0c-630">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="acd0c-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acd0c-631">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acd0c-632">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acd0c-633">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acd0c-634">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acd0c-635">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acd0c-636">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-636">Tap **Languages**.</span></span>

    ![インターネット オプション](localization/_static/lang.png)

3. <span data-ttu-id="acd0c-638">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acd0c-639">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acd0c-640">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-640">Add the language.</span></span>

6. <span data-ttu-id="acd0c-641">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="acd0c-642">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-642">Use a custom provider</span></span>

<span data-ttu-id="acd0c-643">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acd0c-644">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acd0c-645">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="acd0c-646">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acd0c-647">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-647">Set the culture programmatically</span></span>

<span data-ttu-id="acd0c-648">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acd0c-649">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acd0c-650">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acd0c-651">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acd0c-652">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acd0c-653">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acd0c-654">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="acd0c-654">Model binding route data and query strings</span></span>

<span data-ttu-id="acd0c-655">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acd0c-656">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="acd0c-656">Globalization and localization terms</span></span>

<span data-ttu-id="acd0c-657">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acd0c-658">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acd0c-659">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acd0c-660">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acd0c-661">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acd0c-662">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acd0c-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acd0c-664">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acd0c-665">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acd0c-666">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acd0c-667">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acd0c-668">用語:</span><span class="sxs-lookup"><span data-stu-id="acd0c-668">Terms:</span></span>

* <span data-ttu-id="acd0c-669">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acd0c-670">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acd0c-671">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acd0c-672">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acd0c-673">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acd0c-674">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-674">(for example "en", "es")</span></span>
* <span data-ttu-id="acd0c-675">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acd0c-676">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acd0c-677">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acd0c-678">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acd0c-679">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="acd0c-680">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="acd0c-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acd0c-681">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acd0c-682">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="acd0c-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acd0c-683">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="acd0c-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acd0c-684">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="acd0c-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acd0c-685">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="acd0c-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="acd0c-686">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="acd0c-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="acd0c-687">多言語の Web サイトにすると、サイトはより幅広い対象ユーザーにリーチできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="acd0c-688">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="acd0c-689">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="acd0c-690">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="acd0c-691">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="acd0c-692">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="acd0c-693">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="acd0c-694">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-694">App localization involves the following:</span></span>

1. <span data-ttu-id="acd0c-695">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="acd0c-696">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="acd0c-697">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="acd0c-698">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="acd0c-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="acd0c-699">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="acd0c-699">Make the app's content localizable</span></span>

<span data-ttu-id="acd0c-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` では、既定の言語文字列をリソース ファイルに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="acd0c-701">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="acd0c-702">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="acd0c-703">前のコードの `IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)で説明したものです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="acd0c-704">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="acd0c-705">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="acd0c-706">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="acd0c-707">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="acd0c-708">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="acd0c-709">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="acd0c-710">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="acd0c-711">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="acd0c-712">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="acd0c-713">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-714">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="acd0c-715">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="acd0c-716">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="acd0c-717">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="acd0c-718">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="acd0c-719">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="acd0c-720">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-720">View localization</span></span>

<span data-ttu-id="acd0c-721">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="acd0c-722">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="acd0c-723">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="acd0c-724">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="acd0c-725">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="acd0c-726">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="acd0c-727">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="acd0c-728">次の Razor マークアップがあるとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="acd0c-729">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="acd0c-730">Key</span><span class="sxs-lookup"><span data-stu-id="acd0c-730">Key</span></span> | <span data-ttu-id="acd0c-731">[値]</span><span class="sxs-lookup"><span data-stu-id="acd0c-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="acd0c-732">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-733">'Blazor'</span></span>
- <span data-ttu-id="acd0c-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-734">'Identity'</span></span>
- <span data-ttu-id="acd0c-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-736">'Razor'</span></span>
- <span data-ttu-id="acd0c-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-737">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="acd0c-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="acd0c-739">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="acd0c-740">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="acd0c-741">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="acd0c-742">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="acd0c-742">DataAnnotations localization</span></span>

<span data-ttu-id="acd0c-743">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="acd0c-744">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="acd0c-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="acd0c-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="acd0c-747">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="acd0c-748">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="acd0c-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="acd0c-749">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="acd0c-750">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="acd0c-751">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="acd0c-752">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="acd0c-753">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="acd0c-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="acd0c-754">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="acd0c-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="acd0c-755">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="acd0c-756">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="acd0c-757">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="acd0c-758">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="acd0c-759">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="acd0c-760">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="acd0c-761">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="acd0c-762">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="acd0c-763">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="acd0c-764">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="acd0c-764">Resource files</span></span>

<span data-ttu-id="acd0c-765">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="acd0c-766">既定以外の言語の翻訳された文字列は、 *.resx* リソース ファイルで分離されています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="acd0c-767">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="acd0c-768">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="acd0c-769">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="acd0c-770">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]** > **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

2. <span data-ttu-id="acd0c-773">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

3. <span data-ttu-id="acd0c-775">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

    <span data-ttu-id="acd0c-777">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="acd0c-779">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-779">Resource file naming</span></span>

<span data-ttu-id="acd0c-780">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="acd0c-781">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="acd0c-782">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-783">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="acd0c-784">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="acd0c-785">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-786">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="acd0c-787">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-788">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="acd0c-789">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="acd0c-790">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="acd0c-791">リソース名</span><span class="sxs-lookup"><span data-stu-id="acd0c-791">Resource name</span></span> | <span data-ttu-id="acd0c-792">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="acd0c-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="acd0c-793">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-794">'Blazor'</span></span>
- <span data-ttu-id="acd0c-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-795">'Identity'</span></span>
- <span data-ttu-id="acd0c-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-797">'Razor'</span></span>
- <span data-ttu-id="acd0c-798">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-799">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-800">'Blazor'</span></span>
- <span data-ttu-id="acd0c-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-801">'Identity'</span></span>
- <span data-ttu-id="acd0c-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-803">'Razor'</span></span>
- <span data-ttu-id="acd0c-804">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-805">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-806">'Blazor'</span></span>
- <span data-ttu-id="acd0c-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-807">'Identity'</span></span>
- <span data-ttu-id="acd0c-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-809">'Razor'</span></span>
- <span data-ttu-id="acd0c-810">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-811">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-812">'Blazor'</span></span>
- <span data-ttu-id="acd0c-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-813">'Identity'</span></span>
- <span data-ttu-id="acd0c-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-815">'Razor'</span></span>
- <span data-ttu-id="acd0c-816">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-816">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-818">'Blazor'</span></span>
- <span data-ttu-id="acd0c-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-819">'Identity'</span></span>
- <span data-ttu-id="acd0c-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-821">'Razor'</span></span>
- <span data-ttu-id="acd0c-822">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-823">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-824">'Blazor'</span></span>
- <span data-ttu-id="acd0c-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-825">'Identity'</span></span>
- <span data-ttu-id="acd0c-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-827">'Razor'</span></span>
- <span data-ttu-id="acd0c-828">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-829">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-830">'Blazor'</span></span>
- <span data-ttu-id="acd0c-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-831">'Identity'</span></span>
- <span data-ttu-id="acd0c-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-833">'Razor'</span></span>
- <span data-ttu-id="acd0c-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acd0c-835">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acd0c-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="acd0c-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-836">'Blazor'</span></span>
- <span data-ttu-id="acd0c-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acd0c-837">'Identity'</span></span>
- <span data-ttu-id="acd0c-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acd0c-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="acd0c-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acd0c-839">'Razor'</span></span>
- <span data-ttu-id="acd0c-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acd0c-840">'SignalR' uid:</span></span> 

<span data-ttu-id="acd0c-841">------- | | Resources/Controllers.HomeController.fr.resx | ドット  | | Resources/Controllers/HomeController.fr.resx  | パス | |    |     |</span><span class="sxs-lookup"><span data-stu-id="acd0c-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="acd0c-842">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="acd0c-843">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="acd0c-844"> ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="acd0c-845">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="acd0c-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="acd0c-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="acd0c-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="acd0c-848">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="acd0c-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="acd0c-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="acd0c-850">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="acd0c-851">これは、プロジェクト名が有効な .NET 識別子でない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="acd0c-852">たとえば、`my-project-name.csproj` ではルート名前空間 `my_project_name` が使用されるので、アセンブリ名 `my-project-name` はこのエラーにつながります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="acd0c-853">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="acd0c-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="acd0c-854">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-854">Localization does not work by default.</span></span>
* <span data-ttu-id="acd0c-855">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="acd0c-856">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="acd0c-857">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="acd0c-858">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="acd0c-859">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="acd0c-859">Culture fallback behavior</span></span>

<span data-ttu-id="acd0c-860">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="acd0c-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="acd0c-861">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="acd0c-862">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="acd0c-863">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="acd0c-864">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="acd0c-865">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="acd0c-866">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="acd0c-867">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="acd0c-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="acd0c-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="acd0c-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="acd0c-870">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="acd0c-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="acd0c-871">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="acd0c-872">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="acd0c-873">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="acd0c-874">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="acd0c-875">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="acd0c-876">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="acd0c-877">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="acd0c-878">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="acd0c-879">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="acd0c-880">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="acd0c-880">Add other cultures</span></span>

<span data-ttu-id="acd0c-881">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="acd0c-882">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="acd0c-883">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="acd0c-884">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="acd0c-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="acd0c-885">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="acd0c-885">Configure localization</span></span>

<span data-ttu-id="acd0c-886">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="acd0c-887">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="acd0c-888">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="acd0c-889">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="acd0c-890">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="acd0c-891">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="acd0c-892">`AddDataAnnotationsLocalization``IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="acd0c-893">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="acd0c-893">Localization middleware</span></span>

<span data-ttu-id="acd0c-894">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="acd0c-895">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="acd0c-896">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="acd0c-897">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="acd0c-898">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="acd0c-899">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="acd0c-900">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="acd0c-901">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="acd0c-902">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="acd0c-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-904">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="acd0c-905">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="acd0c-906">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="acd0c-907">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="acd0c-908">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="acd0c-909">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="acd0c-910">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="acd0c-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="acd0c-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="acd0c-912">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="acd0c-913">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="acd0c-914">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="acd0c-915">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="acd0c-916">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="acd0c-917">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="acd0c-918">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="acd0c-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="acd0c-919">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="acd0c-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="acd0c-920">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="acd0c-921">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="acd0c-922">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="acd0c-923">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="acd0c-924">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="acd0c-925">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-925">Tap **Languages**.</span></span>

    ![インターネット オプション](localization/_static/lang.png)

3. <span data-ttu-id="acd0c-927">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="acd0c-928">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="acd0c-929">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-929">Add the language.</span></span>

6. <span data-ttu-id="acd0c-930">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="acd0c-931">Content-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="acd0c-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="acd0c-932">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) エンティティ ヘッダーは、</span><span class="sxs-lookup"><span data-stu-id="acd0c-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="acd0c-933">対象ユーザー用に想定した言語を説明するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="acd0c-934">ユーザーが、そのユーザー独自の優先言語に従って区別することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="acd0c-935">エンティティ ヘッダーは、HTTP 要求と応答の両方で使用されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="acd0c-936">プロパティ `ApplyCurrentCultureToResponseHeaders` を設定することによって `Content-Language` ヘッダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="acd0c-937">`Content-Language` ヘッダーを追加すると、</span><span class="sxs-lookup"><span data-stu-id="acd0c-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="acd0c-938">RequestLocalizationMiddleware で `CurrentUICulture` を使って `Content-Language` を設定できるようになります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="acd0c-939">応答ヘッダーの `Content-Language` を明示的に設定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="acd0c-940">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="acd0c-940">Use a custom provider</span></span>

<span data-ttu-id="acd0c-941">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="acd0c-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="acd0c-942">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="acd0c-943">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="acd0c-944">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="acd0c-945">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="acd0c-945">Set the culture programmatically</span></span>

<span data-ttu-id="acd0c-946">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="acd0c-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="acd0c-947">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="acd0c-948">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="acd0c-949">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="acd0c-950">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="acd0c-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="acd0c-951">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` をフローするコードがあります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="acd0c-952">モデル バインド ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="acd0c-952">Model binding route data and query strings</span></span>

<span data-ttu-id="acd0c-953">「[モデル バインド ルート データとクエリ文字列のグローバリゼーション動作](xref:mvc/models/model-binding#glob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="acd0c-954">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="acd0c-954">Globalization and localization terms</span></span>

<span data-ttu-id="acd0c-955">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="acd0c-956">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="acd0c-957">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="acd0c-958">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="acd0c-959">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="acd0c-960">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="acd0c-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="acd0c-962">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="acd0c-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="acd0c-963">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="acd0c-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="acd0c-964">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="acd0c-965">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="acd0c-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="acd0c-966">用語:</span><span class="sxs-lookup"><span data-stu-id="acd0c-966">Terms:</span></span>

* <span data-ttu-id="acd0c-967">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="acd0c-968">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="acd0c-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="acd0c-969">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="acd0c-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="acd0c-970">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="acd0c-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="acd0c-971">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="acd0c-972">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-972">(for example "en", "es")</span></span>
* <span data-ttu-id="acd0c-973">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="acd0c-974">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="acd0c-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="acd0c-975">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="acd0c-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="acd0c-976">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="acd0c-977">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="acd0c-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="acd0c-978">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="acd0c-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="acd0c-979">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="acd0c-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="acd0c-980">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="acd0c-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="acd0c-981">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="acd0c-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="acd0c-982">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="acd0c-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="acd0c-983">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="acd0c-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
