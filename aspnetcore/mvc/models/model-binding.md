---
title: ASP.NET Core でのモデル バインド
author: rick-anderson
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/models/model-binding
ms.openlocfilehash: 49300d32096e577db9b13a0510cc310b91ddb51d
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365354"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="a0ced-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="a0ced-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0ced-104">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="a0ced-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a0ced-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="a0ced-106">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="a0ced-106">What is Model binding</span></span>

<span data-ttu-id="a0ced-107">コントローラーと Razor ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="a0ced-108">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="a0ced-109">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="a0ced-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="a0ced-110">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-110">Model binding automates this process.</span></span> <span data-ttu-id="a0ced-111">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-111">The model binding system:</span></span>

* <span data-ttu-id="a0ced-112">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="a0ced-113">Razorメソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="a0ced-114">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="a0ced-115">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="a0ced-116">例</span><span class="sxs-lookup"><span data-stu-id="a0ced-116">Example</span></span>

<span data-ttu-id="a0ced-117">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="a0ced-118">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="a0ced-119">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="a0ced-120">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="a0ced-121">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="a0ced-122">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="a0ced-123">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="a0ced-124">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="a0ced-125">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="a0ced-126">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="a0ced-127">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="a0ced-128">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="a0ced-129">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="a0ced-130">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="a0ced-131">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="a0ced-132">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="a0ced-133">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="a0ced-133">Targets</span></span>

<span data-ttu-id="a0ced-134">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="a0ced-135">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a0ced-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="a0ced-136">Razor要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a0ced-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="a0ced-137">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a0ced-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="a0ced-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-138">[BindProperty] attribute</span></span>

<span data-ttu-id="a0ced-139">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="a0ced-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-140">[BindProperties] attribute</span></span>

<span data-ttu-id="a0ced-141">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="a0ced-142">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="a0ced-143">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="a0ced-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="a0ced-144">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="a0ced-145">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="a0ced-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="a0ced-146">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="a0ced-147">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="a0ced-148">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="a0ced-149">変換元</span><span class="sxs-lookup"><span data-stu-id="a0ced-149">Sources</span></span>

<span data-ttu-id="a0ced-150">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="a0ced-151">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="a0ced-151">Form fields</span></span>
1. <span data-ttu-id="a0ced-152">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="a0ced-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="a0ced-153">ルート データ</span><span class="sxs-lookup"><span data-stu-id="a0ced-153">Route data</span></span>
1. <span data-ttu-id="a0ced-154">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="a0ced-154">Query string parameters</span></span>
1. <span data-ttu-id="a0ced-155">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="a0ced-155">Uploaded files</span></span>

<span data-ttu-id="a0ced-156">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="a0ced-157">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-157">There are a few exceptions:</span></span>

* <span data-ttu-id="a0ced-158">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="a0ced-159">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="a0ced-160">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="a0ced-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="a0ced-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="a0ced-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="a0ced-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="a0ced-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="a0ced-166">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="a0ced-166">These attributes:</span></span>

* <span data-ttu-id="a0ced-167">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="a0ced-168">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="a0ced-169">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="a0ced-170">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="a0ced-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-171">[FromBody] attribute</span></span>

<span data-ttu-id="a0ced-172">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="a0ced-173">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="a0ced-174">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="a0ced-175">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="a0ced-176">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="a0ced-177">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="a0ced-178">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="a0ced-178">In the preceding example:</span></span>

* <span data-ttu-id="a0ced-179">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="a0ced-180">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="a0ced-181">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="a0ced-182">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="a0ced-183">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="a0ced-184">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="a0ced-185">その他のソース</span><span class="sxs-lookup"><span data-stu-id="a0ced-185">Additional sources</span></span>

<span data-ttu-id="a0ced-186">ソース データは、" *値プロバイダー* " によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="a0ced-187">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="a0ced-188">たとえば、またはセッション状態のデータが必要になる場合があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="a0ced-189">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="a0ced-189">To get data from a new source:</span></span>

* <span data-ttu-id="a0ced-190">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="a0ced-191">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="a0ced-192">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="a0ced-193">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) の例が含まれてい cookie ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="a0ced-194">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="a0ced-195">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="a0ced-196">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="a0ced-197">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="a0ced-197">No source for a model property</span></span>

<span data-ttu-id="a0ced-198">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="a0ced-199">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="a0ced-200">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="a0ced-201">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="a0ced-202">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="a0ced-203">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="a0ced-204">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="a0ced-205">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="a0ced-206">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="a0ced-207">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="a0ced-208">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="a0ced-208">Type conversion errors</span></span>

<span data-ttu-id="a0ced-209">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="a0ced-210">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="a0ced-211">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="a0ced-212">ページで、次のエラーメッセージが表示さ Razor れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="a0ced-213">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ Razor します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="a0ced-214">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="a0ced-215">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="a0ced-216">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="a0ced-217">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="a0ced-218">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="a0ced-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="a0ced-219">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="a0ced-220">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="a0ced-221">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="a0ced-222">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="a0ced-223">単純型</span><span class="sxs-lookup"><span data-stu-id="a0ced-223">Simple types</span></span>

<span data-ttu-id="a0ced-224">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="a0ced-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="a0ced-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="a0ced-226">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="a0ced-227">Char</span><span class="sxs-lookup"><span data-stu-id="a0ced-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="a0ced-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="a0ced-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="a0ced-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="a0ced-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="a0ced-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="a0ced-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="a0ced-231">Double</span><span class="sxs-lookup"><span data-stu-id="a0ced-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="a0ced-232">Enum</span><span class="sxs-lookup"><span data-stu-id="a0ced-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="a0ced-233">Guid</span><span class="sxs-lookup"><span data-stu-id="a0ced-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="a0ced-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="a0ced-235">Single</span><span class="sxs-lookup"><span data-stu-id="a0ced-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="a0ced-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="a0ced-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="a0ced-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="a0ced-238">Uri</span><span class="sxs-lookup"><span data-stu-id="a0ced-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="a0ced-239">Version</span><span class="sxs-lookup"><span data-stu-id="a0ced-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="a0ced-240">複合型</span><span class="sxs-lookup"><span data-stu-id="a0ced-240">Complex types</span></span>

<span data-ttu-id="a0ced-241">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="a0ced-242">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="a0ced-243">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="a0ced-244">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="a0ced-245">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="a0ced-246">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="a0ced-247">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="a0ced-248">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="a0ced-249">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="a0ced-249">Prefix = parameter name</span></span>

<span data-ttu-id="a0ced-250">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="a0ced-251">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="a0ced-252">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="a0ced-253">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="a0ced-253">Prefix = property name</span></span>

<span data-ttu-id="a0ced-254">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="a0ced-255">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a0ced-256">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="a0ced-257">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="a0ced-257">Custom prefix</span></span>

<span data-ttu-id="a0ced-258">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="a0ced-259">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a0ced-260">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="a0ced-261">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-261">Attributes for complex type targets</span></span>

<span data-ttu-id="a0ced-262">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="a0ced-263">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="a0ced-264">これらは、入力フォーマッタに影響を与え **ません** 。これは、ポストされた JSON および XML 要求本文を処理します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="a0ced-265">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="a0ced-266">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-266">[Bind] attribute</span></span>

<span data-ttu-id="a0ced-267">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="a0ced-268">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="a0ced-269">`[Bind]` 入力フォーマッタには影響し _\*_ません_\*_ 。</span><span class="sxs-lookup"><span data-stu-id="a0ced-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="a0ced-270">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="a0ced-271">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="a0ced-272">`[Bind]`属性を使用して、_create \* シナリオで過剰ポストを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="a0ced-273">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="a0ced-274">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="a0ced-275">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="a0ced-276">[ModelBinder] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="a0ced-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 型、プロパティ、またはパラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="a0ced-278">特定のインスタンスまたは型をバインドするために使用するモデルバインダーの種類を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="a0ced-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="a0ced-280">属性を使用して、 `[ModelBinder]` モデルがバインドされているときに、プロパティまたはパラメーターの名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="a0ced-281">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-281">[BindRequired] attribute</span></span>

<span data-ttu-id="a0ced-282">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a0ced-283">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="a0ced-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="a0ced-285">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="a0ced-286">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-286">[BindNever] attribute</span></span>

<span data-ttu-id="a0ced-287">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a0ced-288">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="a0ced-289">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="a0ced-290">コレクション</span><span class="sxs-lookup"><span data-stu-id="a0ced-290">Collections</span></span>

<span data-ttu-id="a0ced-291">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a0ced-292">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a0ced-293">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-293">For example:</span></span>

* <span data-ttu-id="a0ced-294">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="a0ced-295">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="a0ced-296">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="a0ced-297">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a0ced-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="a0ced-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="a0ced-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="a0ced-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="a0ced-300">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="a0ced-301">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="a0ced-302">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="a0ced-303">辞書</span><span class="sxs-lookup"><span data-stu-id="a0ced-303">Dictionaries</span></span>

<span data-ttu-id="a0ced-304">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a0ced-305">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a0ced-306">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-306">For example:</span></span>

* <span data-ttu-id="a0ced-307">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="a0ced-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="a0ced-308">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="a0ced-309">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a0ced-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="a0ced-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="a0ced-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="a0ced-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="a0ced-312">コンストラクターのバインドとレコードの種類</span><span class="sxs-lookup"><span data-stu-id="a0ced-312">Constructor binding and record types</span></span>

<span data-ttu-id="a0ced-313">モデルバインドでは、複合型にパラメーターなしのコンストラクターが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="a0ced-314">`System.Text.Json`と `Newtonsoft.Json` ベースの入力フォーマッタはどちらも、パラメーターなしのコンストラクターを持たないクラスの逆シリアル化をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="a0ced-315">C# 9 では、レコードの種類が導入されています。これは、ネットワーク上でデータを簡潔に表現するための優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="a0ced-316">ASP.NET Core は、1つのコンストラクターを使用して、モデルバインドとレコードの種類の検証のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="a0ced-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="a0ced-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="a0ced-318">レコードの種類を検証するとき、ランタイムは、プロパティではなく、パラメーターに対して明示的に検証メタデータを検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="a0ced-319">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="a0ced-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="a0ced-320">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="a0ced-321">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="a0ced-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="a0ced-322">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="a0ced-323">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="a0ced-324">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="a0ced-325">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="a0ced-326"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="a0ced-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="a0ced-327">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="a0ced-328">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="a0ced-329">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="a0ced-330">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="a0ced-330">Special data types</span></span>

<span data-ttu-id="a0ced-331">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="a0ced-332">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="a0ced-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="a0ced-333">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="a0ced-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="a0ced-334">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="a0ced-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="a0ced-335">CancellationToken</span></span>

<span data-ttu-id="a0ced-336">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-336">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="a0ced-337">FormCollection</span><span class="sxs-lookup"><span data-stu-id="a0ced-337">FormCollection</span></span>

<span data-ttu-id="a0ced-338">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-338">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="a0ced-339">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="a0ced-339">Input formatters</span></span>

<span data-ttu-id="a0ced-340">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-340">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="a0ced-341">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された " *入力フォーマッタ* " が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-341">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="a0ced-342">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-342">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="a0ced-343">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-343">You can add other formatters for other content types.</span></span>

<span data-ttu-id="a0ced-344">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-344">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="a0ced-345">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-345">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="a0ced-346">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="a0ced-346">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="a0ced-347">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-347">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="a0ced-348">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-348">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="a0ced-349">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-349">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="a0ced-350">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-350">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="a0ced-351">入力フォーマッタを使用してモデル バインドをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="a0ced-351">Customize model binding with input formatters</span></span>

<span data-ttu-id="a0ced-352">入力フォーマッタは、要求本文からデータを読み取るためのすべての役割を担います。</span><span class="sxs-lookup"><span data-stu-id="a0ced-352">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="a0ced-353">このプロセスをカスタマイズするには、入力フォーマッタによって使用される API を構成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-353">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="a0ced-354">このセクションでは、`ObjectId` という名前のカスタム型を理解するために、`System.Text.Json` ベースの入力フォーマッタをカスタマイズする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-354">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="a0ced-355">`Id` という名前のカスタム `ObjectId` プロパティが含まれている、次のモデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-355">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="a0ced-356">`System.Text.Json` を使用する際のモデル バインド プロセスをカスタマイズするために、<xref:System.Text.Json.Serialization.JsonConverter%601> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-356">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="a0ced-357">カスタム コンバーターを使用するために、型に <xref:System.Text.Json.Serialization.JsonConverterAttribute> 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-357">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="a0ced-358">次の例では、`ObjectId` 型は、`ObjectIdConverter` をそのカスタム コンバーターとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-358">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="a0ced-359">詳細については、[カスタム コンバーターを記述する方法](/dotnet/standard/serialization/system-text-json-converters-how-to)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-359">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="a0ced-360">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="a0ced-360">Exclude specified types from model binding</span></span>

<span data-ttu-id="a0ced-361">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-361">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="a0ced-362">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-362">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="a0ced-363">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-363">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="a0ced-364">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-364">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0ced-365">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-365">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="a0ced-366">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-366">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0ced-367">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-367">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="a0ced-368">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="a0ced-368">Custom model binders</span></span>

<span data-ttu-id="a0ced-369">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-369">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="a0ced-370">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-370">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="a0ced-371">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="a0ced-371">Manual model binding</span></span> 

<span data-ttu-id="a0ced-372">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-372">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="a0ced-373">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-373">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="a0ced-374">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-374">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="a0ced-375">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-375">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="a0ced-376">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-376">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="a0ced-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> では、フォーム本文、クエリ文字列、およびルート データからデータを取得するために、値プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="a0ced-378">`TryUpdateModelAsync` は通常、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-378">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="a0ced-379">Razor過剰ポストを防ぐために、コントローラーとビューを使用してページおよび MVC アプリで使用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-379">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="a0ced-380">フォーム データ、クエリ文字列、およびルート データから使用される場合を除き、Web API では使用されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-380">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="a0ced-381">JSON を使用する Web API エンドポイントでは、[入力フォーマッタ](#input-formatters)を使用して要求本文がオブジェクトに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-381">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="a0ced-382">詳細については、「[TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-382">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="a0ced-383">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-383">[FromServices] attribute</span></span>

<span data-ttu-id="a0ced-384">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a0ced-384">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="a0ced-385">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-385">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="a0ced-386">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-386">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0ced-387">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-387">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0ced-388">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a0ced-388">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0ced-389">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-389">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="a0ced-390">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a0ced-390">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="a0ced-391">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="a0ced-391">What is Model binding</span></span>

<span data-ttu-id="a0ced-392">コントローラーと Razor ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-392">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="a0ced-393">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-393">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="a0ced-394">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="a0ced-394">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="a0ced-395">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-395">Model binding automates this process.</span></span> <span data-ttu-id="a0ced-396">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-396">The model binding system:</span></span>

* <span data-ttu-id="a0ced-397">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-397">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="a0ced-398">Razorメソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-398">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="a0ced-399">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-399">Converts string data to .NET types.</span></span>
* <span data-ttu-id="a0ced-400">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-400">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="a0ced-401">例</span><span class="sxs-lookup"><span data-stu-id="a0ced-401">Example</span></span>

<span data-ttu-id="a0ced-402">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-402">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="a0ced-403">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-403">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="a0ced-404">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-404">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="a0ced-405">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-405">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="a0ced-406">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-406">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="a0ced-407">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-407">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="a0ced-408">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-408">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="a0ced-409">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-409">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="a0ced-410">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-410">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="a0ced-411">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-411">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="a0ced-412">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-412">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="a0ced-413">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-413">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="a0ced-414">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-414">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="a0ced-415">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-415">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="a0ced-416">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-416">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="a0ced-417">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-417">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="a0ced-418">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="a0ced-418">Targets</span></span>

<span data-ttu-id="a0ced-419">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-419">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="a0ced-420">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a0ced-420">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="a0ced-421">Razor要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a0ced-421">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="a0ced-422">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a0ced-422">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="a0ced-423">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-423">[BindProperty] attribute</span></span>

<span data-ttu-id="a0ced-424">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-424">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="a0ced-425">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-425">[BindProperties] attribute</span></span>

<span data-ttu-id="a0ced-426">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-426">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="a0ced-427">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-427">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="a0ced-428">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="a0ced-428">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="a0ced-429">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-429">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="a0ced-430">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="a0ced-430">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="a0ced-431">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-431">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="a0ced-432">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-432">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="a0ced-433">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-433">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="a0ced-434">変換元</span><span class="sxs-lookup"><span data-stu-id="a0ced-434">Sources</span></span>

<span data-ttu-id="a0ced-435">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-435">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="a0ced-436">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="a0ced-436">Form fields</span></span>
1. <span data-ttu-id="a0ced-437">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="a0ced-437">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="a0ced-438">ルート データ</span><span class="sxs-lookup"><span data-stu-id="a0ced-438">Route data</span></span>
1. <span data-ttu-id="a0ced-439">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="a0ced-439">Query string parameters</span></span>
1. <span data-ttu-id="a0ced-440">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="a0ced-440">Uploaded files</span></span>

<span data-ttu-id="a0ced-441">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-441">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="a0ced-442">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-442">There are a few exceptions:</span></span>

* <span data-ttu-id="a0ced-443">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-443">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="a0ced-444">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-444">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="a0ced-445">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-445">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="a0ced-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="a0ced-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="a0ced-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="a0ced-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="a0ced-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="a0ced-451">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="a0ced-451">These attributes:</span></span>

* <span data-ttu-id="a0ced-452">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-452">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="a0ced-453">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-453">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="a0ced-454">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-454">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="a0ced-455">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-455">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="a0ced-456">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-456">[FromBody] attribute</span></span>

<span data-ttu-id="a0ced-457">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-457">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="a0ced-458">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-458">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="a0ced-459">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-459">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="a0ced-460">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-460">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="a0ced-461">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-461">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="a0ced-462">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-462">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="a0ced-463">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="a0ced-463">In the preceding example:</span></span>

* <span data-ttu-id="a0ced-464">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-464">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="a0ced-465">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-465">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="a0ced-466">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-466">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="a0ced-467">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-467">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="a0ced-468">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-468">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="a0ced-469">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-469">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="a0ced-470">その他のソース</span><span class="sxs-lookup"><span data-stu-id="a0ced-470">Additional sources</span></span>

<span data-ttu-id="a0ced-471">ソース データは、" *値プロバイダー* " によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-471">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="a0ced-472">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-472">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="a0ced-473">たとえば、またはセッション状態のデータが必要になる場合があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-473">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="a0ced-474">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="a0ced-474">To get data from a new source:</span></span>

* <span data-ttu-id="a0ced-475">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-475">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="a0ced-476">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-476">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="a0ced-477">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-477">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="a0ced-478">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) の例が含まれてい cookie ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-478">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="a0ced-479">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-479">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="a0ced-480">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-480">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="a0ced-481">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-481">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="a0ced-482">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="a0ced-482">No source for a model property</span></span>

<span data-ttu-id="a0ced-483">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-483">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="a0ced-484">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-484">The property is set to null or a default value:</span></span>

* <span data-ttu-id="a0ced-485">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-485">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="a0ced-486">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-486">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="a0ced-487">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-487">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="a0ced-488">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-488">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="a0ced-489">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-489">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="a0ced-490">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-490">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="a0ced-491">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-491">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="a0ced-492">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-492">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="a0ced-493">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="a0ced-493">Type conversion errors</span></span>

<span data-ttu-id="a0ced-494">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-494">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="a0ced-495">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-495">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="a0ced-496">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-496">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="a0ced-497">ページで、次のエラーメッセージが表示さ Razor れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-497">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="a0ced-498">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ Razor します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-498">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="a0ced-499">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-499">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="a0ced-500">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-500">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="a0ced-501">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-501">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="a0ced-502">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-502">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="a0ced-503">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="a0ced-503">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="a0ced-504">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-504">The invalid input does appear in an error message.</span></span> <span data-ttu-id="a0ced-505">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-505">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="a0ced-506">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-506">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="a0ced-507">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-507">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="a0ced-508">単純型</span><span class="sxs-lookup"><span data-stu-id="a0ced-508">Simple types</span></span>

<span data-ttu-id="a0ced-509">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-509">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="a0ced-510">Boolean</span><span class="sxs-lookup"><span data-stu-id="a0ced-510">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="a0ced-511">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="a0ced-512">Char</span><span class="sxs-lookup"><span data-stu-id="a0ced-512">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="a0ced-513">DateTime</span><span class="sxs-lookup"><span data-stu-id="a0ced-513">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="a0ced-514">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="a0ced-514">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="a0ced-515">Decimal</span><span class="sxs-lookup"><span data-stu-id="a0ced-515">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="a0ced-516">Double</span><span class="sxs-lookup"><span data-stu-id="a0ced-516">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="a0ced-517">Enum</span><span class="sxs-lookup"><span data-stu-id="a0ced-517">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="a0ced-518">Guid</span><span class="sxs-lookup"><span data-stu-id="a0ced-518">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="a0ced-519">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="a0ced-520">Single</span><span class="sxs-lookup"><span data-stu-id="a0ced-520">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="a0ced-521">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="a0ced-521">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="a0ced-522">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="a0ced-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="a0ced-523">Uri</span><span class="sxs-lookup"><span data-stu-id="a0ced-523">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="a0ced-524">Version</span><span class="sxs-lookup"><span data-stu-id="a0ced-524">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="a0ced-525">複合型</span><span class="sxs-lookup"><span data-stu-id="a0ced-525">Complex types</span></span>

<span data-ttu-id="a0ced-526">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-526">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="a0ced-527">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-527">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="a0ced-528">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-528">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="a0ced-529">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-529">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="a0ced-530">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-530">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="a0ced-531">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="a0ced-531">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="a0ced-532">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-532">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="a0ced-533">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-533">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="a0ced-534">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="a0ced-534">Prefix = parameter name</span></span>

<span data-ttu-id="a0ced-535">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-535">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="a0ced-536">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-536">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="a0ced-537">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-537">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="a0ced-538">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="a0ced-538">Prefix = property name</span></span>

<span data-ttu-id="a0ced-539">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-539">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="a0ced-540">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-540">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a0ced-541">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-541">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="a0ced-542">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="a0ced-542">Custom prefix</span></span>

<span data-ttu-id="a0ced-543">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-543">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="a0ced-544">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-544">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="a0ced-545">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-545">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="a0ced-546">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-546">Attributes for complex type targets</span></span>

<span data-ttu-id="a0ced-547">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-547">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="a0ced-548">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-548">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="a0ced-549">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-549">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="a0ced-550">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-550">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="a0ced-551">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-551">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="a0ced-552">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-552">[BindRequired] attribute</span></span>

<span data-ttu-id="a0ced-553">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-553">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a0ced-554">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-554">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="a0ced-555">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-555">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="a0ced-556">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-556">[BindNever] attribute</span></span>

<span data-ttu-id="a0ced-557">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-557">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="a0ced-558">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-558">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="a0ced-559">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-559">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="a0ced-560">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-560">[Bind] attribute</span></span>

<span data-ttu-id="a0ced-561">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-561">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="a0ced-562">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-562">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="a0ced-563">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-563">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="a0ced-564">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-564">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="a0ced-565">`[Bind]` 属性を使用すれば、" *作成* " シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-565">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="a0ced-566">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-566">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="a0ced-567">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-567">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="a0ced-568">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-568">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="a0ced-569">コレクション</span><span class="sxs-lookup"><span data-stu-id="a0ced-569">Collections</span></span>

<span data-ttu-id="a0ced-570">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-570">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a0ced-571">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a0ced-572">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-572">For example:</span></span>

* <span data-ttu-id="a0ced-573">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="a0ced-573">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="a0ced-574">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-574">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="a0ced-575">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-575">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="a0ced-576">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-576">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a0ced-577">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="a0ced-577">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="a0ced-578">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="a0ced-578">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="a0ced-579">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-579">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="a0ced-580">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-580">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="a0ced-581">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-581">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="a0ced-582">辞書</span><span class="sxs-lookup"><span data-stu-id="a0ced-582">Dictionaries</span></span>

<span data-ttu-id="a0ced-583">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-583">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="a0ced-584">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-584">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="a0ced-585">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-585">For example:</span></span>

* <span data-ttu-id="a0ced-586">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="a0ced-586">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="a0ced-587">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-587">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="a0ced-588">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-588">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="a0ced-589">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="a0ced-589">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="a0ced-590">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="a0ced-590">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="a0ced-591">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="a0ced-591">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="a0ced-592">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-592">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="a0ced-593">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="a0ced-593">Treat values as invariant culture.</span></span>
* <span data-ttu-id="a0ced-594">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-594">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="a0ced-595">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-595">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="a0ced-596">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-596">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="a0ced-597">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-597">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="a0ced-598"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="a0ced-598">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="a0ced-599">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-599">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="a0ced-600">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-600">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="a0ced-601">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-601">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="a0ced-602">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="a0ced-602">Special data types</span></span>

<span data-ttu-id="a0ced-603">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-603">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="a0ced-604">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="a0ced-604">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="a0ced-605">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="a0ced-605">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="a0ced-606">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-606">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="a0ced-607">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="a0ced-607">CancellationToken</span></span>

<span data-ttu-id="a0ced-608">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-608">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="a0ced-609">FormCollection</span><span class="sxs-lookup"><span data-stu-id="a0ced-609">FormCollection</span></span>

<span data-ttu-id="a0ced-610">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-610">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="a0ced-611">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="a0ced-611">Input formatters</span></span>

<span data-ttu-id="a0ced-612">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-612">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="a0ced-613">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された " *入力フォーマッタ* " が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-613">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="a0ced-614">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-614">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="a0ced-615">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-615">You can add other formatters for other content types.</span></span>

<span data-ttu-id="a0ced-616">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-616">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="a0ced-617">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-617">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="a0ced-618">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="a0ced-618">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="a0ced-619">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-619">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="a0ced-620">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-620">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="a0ced-621">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-621">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="a0ced-622">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-622">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="a0ced-623">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="a0ced-623">Exclude specified types from model binding</span></span>

<span data-ttu-id="a0ced-624">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-624">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="a0ced-625">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-625">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="a0ced-626">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-626">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="a0ced-627">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-627">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0ced-628">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-628">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="a0ced-629">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-629">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a0ced-630">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0ced-630">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="a0ced-631">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="a0ced-631">Custom model binders</span></span>

<span data-ttu-id="a0ced-632">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-632">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="a0ced-633">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0ced-633">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="a0ced-634">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="a0ced-634">Manual model binding</span></span>

<span data-ttu-id="a0ced-635">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-635">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="a0ced-636">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="a0ced-636">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="a0ced-637">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-637">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="a0ced-638">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-638">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="a0ced-639">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0ced-639">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="a0ced-640">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="a0ced-640">[FromServices] attribute</span></span>

<span data-ttu-id="a0ced-641">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="a0ced-641">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="a0ced-642">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="a0ced-642">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="a0ced-643">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="a0ced-643">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0ced-644">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="a0ced-644">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0ced-645">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a0ced-645">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
