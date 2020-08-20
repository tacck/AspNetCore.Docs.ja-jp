---
title: ASP.NET Core でのモデル バインド
author: rick-anderson
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ec36ff6d646e0554550a4372389aed89aa267b1f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633982"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="38e20-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="38e20-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38e20-104">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="38e20-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="38e20-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="38e20-106">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="38e20-106">What is Model binding</span></span>

<span data-ttu-id="38e20-107">コントローラーと Razor ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="38e20-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="38e20-108">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="38e20-109">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="38e20-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="38e20-110">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-110">Model binding automates this process.</span></span> <span data-ttu-id="38e20-111">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-111">The model binding system:</span></span>

* <span data-ttu-id="38e20-112">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="38e20-113">Razorメソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="38e20-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="38e20-114">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="38e20-115">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="38e20-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="38e20-116">例</span><span class="sxs-lookup"><span data-stu-id="38e20-116">Example</span></span>

<span data-ttu-id="38e20-117">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="38e20-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="38e20-118">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="38e20-119">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="38e20-120">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="38e20-121">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="38e20-122">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="38e20-123">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="38e20-124">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="38e20-125">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="38e20-126">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="38e20-127">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="38e20-128">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="38e20-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="38e20-129">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="38e20-130">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="38e20-131">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="38e20-132">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="38e20-133">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="38e20-133">Targets</span></span>

<span data-ttu-id="38e20-134">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="38e20-135">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="38e20-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="38e20-136">Razor要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="38e20-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="38e20-137">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="38e20-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="38e20-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-138">[BindProperty] attribute</span></span>

<span data-ttu-id="38e20-139">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="38e20-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="38e20-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-140">[BindProperties] attribute</span></span>

<span data-ttu-id="38e20-141">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="38e20-142">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="38e20-143">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="38e20-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="38e20-144">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="38e20-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="38e20-145">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="38e20-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="38e20-146">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="38e20-147">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="38e20-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="38e20-148">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="38e20-149">ソース</span><span class="sxs-lookup"><span data-stu-id="38e20-149">Sources</span></span>

<span data-ttu-id="38e20-150">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="38e20-151">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="38e20-151">Form fields</span></span>
1. <span data-ttu-id="38e20-152">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="38e20-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="38e20-153">ルート データ</span><span class="sxs-lookup"><span data-stu-id="38e20-153">Route data</span></span>
1. <span data-ttu-id="38e20-154">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="38e20-154">Query string parameters</span></span>
1. <span data-ttu-id="38e20-155">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="38e20-155">Uploaded files</span></span>

<span data-ttu-id="38e20-156">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="38e20-157">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-157">There are a few exceptions:</span></span>

* <span data-ttu-id="38e20-158">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="38e20-159">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="38e20-160">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="38e20-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="38e20-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="38e20-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="38e20-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="38e20-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="38e20-166">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="38e20-166">These attributes:</span></span>

* <span data-ttu-id="38e20-167">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="38e20-168">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="38e20-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="38e20-169">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="38e20-170">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="38e20-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-171">[FromBody] attribute</span></span>

<span data-ttu-id="38e20-172">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="38e20-173">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="38e20-174">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="38e20-175">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="38e20-176">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="38e20-177">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="38e20-178">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="38e20-178">In the preceding example:</span></span>

* <span data-ttu-id="38e20-179">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="38e20-180">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="38e20-181">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="38e20-182">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="38e20-183">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="38e20-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="38e20-184">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="38e20-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="38e20-185">その他のソース</span><span class="sxs-lookup"><span data-stu-id="38e20-185">Additional sources</span></span>

<span data-ttu-id="38e20-186">ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="38e20-187">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="38e20-188">たとえば、またはセッション状態のデータが必要になる場合があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="38e20-189">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="38e20-189">To get data from a new source:</span></span>

* <span data-ttu-id="38e20-190">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="38e20-191">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="38e20-192">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="38e20-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="38e20-193">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) の例が含まれてい cookie ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="38e20-194">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="38e20-195">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="38e20-196">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38e20-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="38e20-197">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="38e20-197">No source for a model property</span></span>

<span data-ttu-id="38e20-198">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="38e20-199">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="38e20-200">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="38e20-201">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="38e20-202">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="38e20-203">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="38e20-204">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="38e20-205">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="38e20-206">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="38e20-207">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="38e20-208">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="38e20-208">Type conversion errors</span></span>

<span data-ttu-id="38e20-209">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="38e20-210">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="38e20-211">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="38e20-212">ページで、次のエラーメッセージが表示さ Razor れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="38e20-213">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ Razor します。</span><span class="sxs-lookup"><span data-stu-id="38e20-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="38e20-214">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="38e20-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="38e20-215">サンプル アプリには、**[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、**[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="38e20-216">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="38e20-217">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="38e20-218">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="38e20-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="38e20-219">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="38e20-220">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="38e20-221">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38e20-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="38e20-222">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="38e20-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="38e20-223">単純型</span><span class="sxs-lookup"><span data-stu-id="38e20-223">Simple types</span></span>

<span data-ttu-id="38e20-224">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="38e20-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="38e20-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="38e20-226">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="38e20-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="38e20-227">Char</span><span class="sxs-lookup"><span data-stu-id="38e20-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="38e20-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="38e20-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="38e20-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="38e20-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="38e20-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="38e20-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="38e20-231">Double</span><span class="sxs-lookup"><span data-stu-id="38e20-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="38e20-232">Enum</span><span class="sxs-lookup"><span data-stu-id="38e20-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="38e20-233">Guid</span><span class="sxs-lookup"><span data-stu-id="38e20-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="38e20-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="38e20-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="38e20-235">Single</span><span class="sxs-lookup"><span data-stu-id="38e20-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="38e20-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="38e20-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="38e20-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="38e20-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="38e20-238">Uri</span><span class="sxs-lookup"><span data-stu-id="38e20-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="38e20-239">Version</span><span class="sxs-lookup"><span data-stu-id="38e20-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="38e20-240">複合型</span><span class="sxs-lookup"><span data-stu-id="38e20-240">Complex types</span></span>

<span data-ttu-id="38e20-241">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="38e20-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="38e20-242">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="38e20-243">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="38e20-244">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="38e20-245">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="38e20-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="38e20-246">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="38e20-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="38e20-247">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="38e20-248">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="38e20-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="38e20-249">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="38e20-249">Prefix = parameter name</span></span>

<span data-ttu-id="38e20-250">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="38e20-251">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="38e20-252">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="38e20-253">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="38e20-253">Prefix = property name</span></span>

<span data-ttu-id="38e20-254">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="38e20-255">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="38e20-256">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="38e20-257">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="38e20-257">Custom prefix</span></span>

<span data-ttu-id="38e20-258">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="38e20-259">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="38e20-260">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="38e20-261">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="38e20-261">Attributes for complex type targets</span></span>

<span data-ttu-id="38e20-262">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="38e20-263">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="38e20-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="38e20-264">これらは、ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響し ***ません*** 。</span><span class="sxs-lookup"><span data-stu-id="38e20-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="38e20-265">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="38e20-266">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-266">[Bind] attribute</span></span>

<span data-ttu-id="38e20-267">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="38e20-268">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="38e20-269">`[Bind]` 入力フォーマッタには影響し ***ません*** 。</span><span class="sxs-lookup"><span data-stu-id="38e20-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="38e20-270">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="38e20-271">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="38e20-272">`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="38e20-273">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="38e20-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="38e20-274">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38e20-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="38e20-275">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="38e20-276">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-276">[BindRequired] attribute</span></span>

<span data-ttu-id="38e20-277">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="38e20-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="38e20-278">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="38e20-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="38e20-280">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="38e20-281">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-281">[BindNever] attribute</span></span>

<span data-ttu-id="38e20-282">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="38e20-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="38e20-283">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="38e20-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="38e20-285">コレクション</span><span class="sxs-lookup"><span data-stu-id="38e20-285">Collections</span></span>

<span data-ttu-id="38e20-286">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="38e20-287">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="38e20-288">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-288">For example:</span></span>

* <span data-ttu-id="38e20-289">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="38e20-290">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-290">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="38e20-291">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="38e20-292">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="38e20-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="38e20-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="38e20-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="38e20-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="38e20-295">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="38e20-296">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="38e20-297">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="38e20-298">ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="38e20-298">Dictionaries</span></span>

<span data-ttu-id="38e20-299">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="38e20-300">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="38e20-301">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-301">For example:</span></span>

* <span data-ttu-id="38e20-302">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="38e20-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="38e20-303">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="38e20-303">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="38e20-304">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="38e20-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="38e20-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="38e20-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="38e20-306">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="38e20-307">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="38e20-307">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="38e20-308">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-308">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="38e20-309">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="38e20-309">Treat values as invariant culture.</span></span>
* <span data-ttu-id="38e20-310">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-310">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="38e20-311">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-311">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="38e20-312">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="38e20-312">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="38e20-313">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-313">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="38e20-314"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="38e20-314">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="38e20-315">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="38e20-315">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="38e20-316">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38e20-316">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="38e20-317">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38e20-317">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="38e20-318">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="38e20-318">Special data types</span></span>

<span data-ttu-id="38e20-319">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-319">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="38e20-320">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="38e20-320">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="38e20-321">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="38e20-321">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="38e20-322">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="38e20-322">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="38e20-323">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="38e20-323">CancellationToken</span></span>

<span data-ttu-id="38e20-324">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-324">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="38e20-325">FormCollection</span><span class="sxs-lookup"><span data-stu-id="38e20-325">FormCollection</span></span>

<span data-ttu-id="38e20-326">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-326">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="38e20-327">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="38e20-327">Input formatters</span></span>

<span data-ttu-id="38e20-328">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-328">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="38e20-329">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-329">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="38e20-330">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="38e20-330">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="38e20-331">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-331">You can add other formatters for other content types.</span></span>

<span data-ttu-id="38e20-332">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-332">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="38e20-333">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-333">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="38e20-334">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="38e20-334">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="38e20-335">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="38e20-335">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="38e20-336">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38e20-336">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="38e20-337">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-337">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="38e20-338">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-338">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="38e20-339">入力フォーマッタを使用してモデル バインドをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="38e20-339">Customize model binding with input formatters</span></span>

<span data-ttu-id="38e20-340">入力フォーマッタは、要求本文からデータを読み取るためのすべての役割を担います。</span><span class="sxs-lookup"><span data-stu-id="38e20-340">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="38e20-341">このプロセスをカスタマイズするには、入力フォーマッタによって使用される API を構成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-341">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="38e20-342">このセクションでは、`ObjectId` という名前のカスタム型を理解するために、`System.Text.Json` ベースの入力フォーマッタをカスタマイズする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-342">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="38e20-343">`Id` という名前のカスタム `ObjectId` プロパティが含まれている、次のモデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="38e20-343">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="38e20-344">`System.Text.Json` を使用する際のモデル バインド プロセスをカスタマイズするために、<xref:System.Text.Json.Serialization.JsonConverter%601> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-344">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="38e20-345">カスタム コンバーターを使用するために、型に <xref:System.Text.Json.Serialization.JsonConverterAttribute> 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-345">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="38e20-346">次の例では、`ObjectId` 型は、`ObjectIdConverter` をそのカスタム コンバーターとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-346">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="38e20-347">詳細については、[カスタム コンバーターを記述する方法](/dotnet/standard/serialization/system-text-json-converters-how-to)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38e20-347">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="38e20-348">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="38e20-348">Exclude specified types from model binding</span></span>

<span data-ttu-id="38e20-349">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-349">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="38e20-350">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="38e20-350">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="38e20-351">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-351">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="38e20-352">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38e20-352">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="38e20-353">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-353">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="38e20-354">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38e20-354">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="38e20-355">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-355">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="38e20-356">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="38e20-356">Custom model binders</span></span>

<span data-ttu-id="38e20-357">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="38e20-357">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="38e20-358">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-358">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="38e20-359">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="38e20-359">Manual model binding</span></span> 

<span data-ttu-id="38e20-360">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-360">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="38e20-361">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-361">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="38e20-362">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-362">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="38e20-363">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-363">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="38e20-364">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-364">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="38e20-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> では、フォーム本文、クエリ文字列、およびルート データからデータを取得するために、値プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="38e20-366">`TryUpdateModelAsync` は通常、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="38e20-366">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="38e20-367">Razor過剰ポストを防ぐために、コントローラーとビューを使用してページおよび MVC アプリで使用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-367">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="38e20-368">フォーム データ、クエリ文字列、およびルート データから使用される場合を除き、Web API では使用されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-368">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="38e20-369">JSON を使用する Web API エンドポイントでは、[入力フォーマッタ](#input-formatters)を使用して要求本文がオブジェクトに逆シリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-369">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="38e20-370">詳細については、「[TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38e20-370">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="38e20-371">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-371">[FromServices] attribute</span></span>

<span data-ttu-id="38e20-372">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="38e20-372">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="38e20-373">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="38e20-373">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="38e20-374">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-374">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="38e20-375">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-375">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38e20-376">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="38e20-376">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38e20-377">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-377">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="38e20-378">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="38e20-378">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="38e20-379">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="38e20-379">What is Model binding</span></span>

<span data-ttu-id="38e20-380">コントローラーと Razor ページは、HTTP 要求から取得されたデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="38e20-380">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="38e20-381">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-381">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="38e20-382">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="38e20-382">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="38e20-383">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-383">Model binding automates this process.</span></span> <span data-ttu-id="38e20-384">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-384">The model binding system:</span></span>

* <span data-ttu-id="38e20-385">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-385">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="38e20-386">Razorメソッドパラメーターおよびパブリックプロパティのデータをコントローラーおよびページに提供します。</span><span class="sxs-lookup"><span data-stu-id="38e20-386">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="38e20-387">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-387">Converts string data to .NET types.</span></span>
* <span data-ttu-id="38e20-388">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="38e20-388">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="38e20-389">例</span><span class="sxs-lookup"><span data-stu-id="38e20-389">Example</span></span>

<span data-ttu-id="38e20-390">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="38e20-390">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="38e20-391">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-391">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="38e20-392">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-392">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="38e20-393">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-393">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="38e20-394">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-394">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="38e20-395">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-395">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="38e20-396">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-396">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="38e20-397">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="38e20-397">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="38e20-398">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-398">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="38e20-399">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="38e20-399">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="38e20-400">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-400">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="38e20-401">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="38e20-401">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="38e20-402">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-402">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="38e20-403">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-403">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="38e20-404">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-404">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="38e20-405">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-405">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="38e20-406">対象サーバー</span><span class="sxs-lookup"><span data-stu-id="38e20-406">Targets</span></span>

<span data-ttu-id="38e20-407">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-407">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="38e20-408">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="38e20-408">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="38e20-409">Razor要求がルーティングされるページハンドラーメソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="38e20-409">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="38e20-410">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="38e20-410">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="38e20-411">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-411">[BindProperty] attribute</span></span>

<span data-ttu-id="38e20-412">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="38e20-412">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="38e20-413">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-413">[BindProperties] attribute</span></span>

<span data-ttu-id="38e20-414">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-414">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="38e20-415">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-415">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="38e20-416">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="38e20-416">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="38e20-417">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="38e20-417">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="38e20-418">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="38e20-418">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="38e20-419">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-419">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="38e20-420">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="38e20-420">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="38e20-421">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-421">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="38e20-422">ソース</span><span class="sxs-lookup"><span data-stu-id="38e20-422">Sources</span></span>

<span data-ttu-id="38e20-423">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-423">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="38e20-424">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="38e20-424">Form fields</span></span>
1. <span data-ttu-id="38e20-425">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="38e20-425">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="38e20-426">ルート データ</span><span class="sxs-lookup"><span data-stu-id="38e20-426">Route data</span></span>
1. <span data-ttu-id="38e20-427">クエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="38e20-427">Query string parameters</span></span>
1. <span data-ttu-id="38e20-428">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="38e20-428">Uploaded files</span></span>

<span data-ttu-id="38e20-429">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-429">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="38e20-430">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-430">There are a few exceptions:</span></span>

* <span data-ttu-id="38e20-431">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-431">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="38e20-432">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-432">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="38e20-433">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-433">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="38e20-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="38e20-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -ルートデータから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="38e20-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -ポストされたフォームフィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="38e20-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="38e20-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="38e20-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="38e20-439">これらの属性: </span><span class="sxs-lookup"><span data-stu-id="38e20-439">These attributes:</span></span>

* <span data-ttu-id="38e20-440">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-440">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="38e20-441">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="38e20-441">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="38e20-442">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-442">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="38e20-443">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-443">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="38e20-444">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-444">[FromBody] attribute</span></span>

<span data-ttu-id="38e20-445">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-445">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="38e20-446">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-446">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="38e20-447">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-447">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="38e20-448">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-448">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="38e20-449">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-449">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="38e20-450">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-450">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="38e20-451">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="38e20-451">In the preceding example:</span></span>

* <span data-ttu-id="38e20-452">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-452">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="38e20-453">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-453">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="38e20-454">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-454">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="38e20-455">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-455">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="38e20-456">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="38e20-456">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="38e20-457">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="38e20-457">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="38e20-458">その他のソース</span><span class="sxs-lookup"><span data-stu-id="38e20-458">Additional sources</span></span>

<span data-ttu-id="38e20-459">ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-459">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="38e20-460">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-460">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="38e20-461">たとえば、またはセッション状態のデータが必要になる場合があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-461">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="38e20-462">新しいソースからデータを取得するには: </span><span class="sxs-lookup"><span data-stu-id="38e20-462">To get data from a new source:</span></span>

* <span data-ttu-id="38e20-463">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-463">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="38e20-464">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="38e20-464">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="38e20-465">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="38e20-465">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="38e20-466">サンプルアプリには、s から値を取得する [値プロバイダー](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) と [ファクトリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) の例が含まれてい cookie ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-466">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="38e20-467">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-467">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="38e20-468">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-468">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="38e20-469">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38e20-469">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="38e20-470">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="38e20-470">No source for a model property</span></span>

<span data-ttu-id="38e20-471">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-471">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="38e20-472">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-472">The property is set to null or a default value:</span></span>

* <span data-ttu-id="38e20-473">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-473">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="38e20-474">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-474">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="38e20-475">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-475">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="38e20-476">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-476">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="38e20-477">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-477">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="38e20-478">モデルプロパティのフォームフィールドに何も見つからない場合にモデルの状態を無効にする必要がある場合は、属性を使用し [`[BindRequired]`](#bindrequired-attribute) ます。</span><span class="sxs-lookup"><span data-stu-id="38e20-478">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="38e20-479">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-479">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="38e20-480">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-480">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="38e20-481">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="38e20-481">Type conversion errors</span></span>

<span data-ttu-id="38e20-482">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-482">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="38e20-483">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-483">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="38e20-484">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-484">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="38e20-485">ページで、次のエラーメッセージが表示さ Razor れたページを再び表示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-485">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="38e20-486">クライアント側の検証では、ページフォームに送信される可能性のあるほとんどの不適切なデータをキャッチ Razor します。</span><span class="sxs-lookup"><span data-stu-id="38e20-486">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="38e20-487">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="38e20-487">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="38e20-488">サンプル アプリには、**[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、**[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-488">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="38e20-489">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-489">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="38e20-490">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="38e20-490">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="38e20-491">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="38e20-491">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="38e20-492">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-492">The invalid input does appear in an error message.</span></span> <span data-ttu-id="38e20-493">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-493">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="38e20-494">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38e20-494">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="38e20-495">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="38e20-495">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="38e20-496">単純型</span><span class="sxs-lookup"><span data-stu-id="38e20-496">Simple types</span></span>

<span data-ttu-id="38e20-497">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-497">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="38e20-498">Boolean</span><span class="sxs-lookup"><span data-stu-id="38e20-498">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="38e20-499">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="38e20-499">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="38e20-500">Char</span><span class="sxs-lookup"><span data-stu-id="38e20-500">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="38e20-501">DateTime</span><span class="sxs-lookup"><span data-stu-id="38e20-501">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="38e20-502">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="38e20-502">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="38e20-503">Decimal</span><span class="sxs-lookup"><span data-stu-id="38e20-503">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="38e20-504">Double</span><span class="sxs-lookup"><span data-stu-id="38e20-504">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="38e20-505">Enum</span><span class="sxs-lookup"><span data-stu-id="38e20-505">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="38e20-506">Guid</span><span class="sxs-lookup"><span data-stu-id="38e20-506">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="38e20-507">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="38e20-507">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="38e20-508">Single</span><span class="sxs-lookup"><span data-stu-id="38e20-508">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="38e20-509">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="38e20-509">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="38e20-510">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="38e20-510">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="38e20-511">Uri</span><span class="sxs-lookup"><span data-stu-id="38e20-511">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="38e20-512">Version</span><span class="sxs-lookup"><span data-stu-id="38e20-512">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="38e20-513">複合型</span><span class="sxs-lookup"><span data-stu-id="38e20-513">Complex types</span></span>

<span data-ttu-id="38e20-514">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="38e20-514">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="38e20-515">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-515">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="38e20-516">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-516">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="38e20-517">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-517">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="38e20-518">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="38e20-518">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="38e20-519">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="38e20-519">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="38e20-520">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-520">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="38e20-521">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="38e20-521">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="38e20-522">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="38e20-522">Prefix = parameter name</span></span>

<span data-ttu-id="38e20-523">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-523">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="38e20-524">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-524">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="38e20-525">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-525">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="38e20-526">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="38e20-526">Prefix = property name</span></span>

<span data-ttu-id="38e20-527">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-527">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="38e20-528">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-528">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="38e20-529">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-529">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="38e20-530">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="38e20-530">Custom prefix</span></span>

<span data-ttu-id="38e20-531">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-531">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="38e20-532">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="38e20-532">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="38e20-533">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-533">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="38e20-534">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="38e20-534">Attributes for complex type targets</span></span>

<span data-ttu-id="38e20-535">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-535">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="38e20-536">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="38e20-536">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="38e20-537">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="38e20-537">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="38e20-538">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="38e20-538">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="38e20-539">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-539">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="38e20-540">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-540">[BindRequired] attribute</span></span>

<span data-ttu-id="38e20-541">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="38e20-541">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="38e20-542">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-542">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="38e20-543">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-543">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="38e20-544">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-544">[BindNever] attribute</span></span>

<span data-ttu-id="38e20-545">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="38e20-545">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="38e20-546">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-546">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="38e20-547">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-547">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="38e20-548">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-548">[Bind] attribute</span></span>

<span data-ttu-id="38e20-549">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-549">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="38e20-550">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-550">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="38e20-551">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-551">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="38e20-552">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-552">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="38e20-553">`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-553">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="38e20-554">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="38e20-554">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="38e20-555">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38e20-555">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="38e20-556">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-556">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="38e20-557">コレクション</span><span class="sxs-lookup"><span data-stu-id="38e20-557">Collections</span></span>

<span data-ttu-id="38e20-558">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-558">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="38e20-559">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-559">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="38e20-560">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-560">For example:</span></span>

* <span data-ttu-id="38e20-561">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合: </span><span class="sxs-lookup"><span data-stu-id="38e20-561">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="38e20-562">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-562">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="38e20-563">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="38e20-563">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="38e20-564">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-564">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="38e20-565">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="38e20-565">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="38e20-566">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="38e20-566">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="38e20-567">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="38e20-567">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="38e20-568">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-568">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="38e20-569">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-569">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="38e20-570">ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="38e20-570">Dictionaries</span></span>

<span data-ttu-id="38e20-571">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-571">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="38e20-572">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-572">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="38e20-573">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-573">For example:</span></span>

* <span data-ttu-id="38e20-574">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします: </span><span class="sxs-lookup"><span data-stu-id="38e20-574">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="38e20-575">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="38e20-575">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="38e20-576">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-576">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="38e20-577">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="38e20-577">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="38e20-578">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="38e20-578">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="38e20-579">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="38e20-579">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="38e20-580">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="38e20-580">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="38e20-581">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="38e20-581">Treat values as invariant culture.</span></span>
* <span data-ttu-id="38e20-582">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="38e20-582">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="38e20-583">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="38e20-583">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="38e20-584">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="38e20-584">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="38e20-585">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-585">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="38e20-586"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="38e20-586">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="38e20-587">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="38e20-587">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="38e20-588">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38e20-588">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="38e20-589">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38e20-589">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="38e20-590">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="38e20-590">Special data types</span></span>

<span data-ttu-id="38e20-591">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-591">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="38e20-592">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="38e20-592">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="38e20-593">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="38e20-593">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="38e20-594">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="38e20-594">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="38e20-595">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="38e20-595">CancellationToken</span></span>

<span data-ttu-id="38e20-596">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-596">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="38e20-597">FormCollection</span><span class="sxs-lookup"><span data-stu-id="38e20-597">FormCollection</span></span>

<span data-ttu-id="38e20-598">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-598">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="38e20-599">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="38e20-599">Input formatters</span></span>

<span data-ttu-id="38e20-600">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-600">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="38e20-601">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-601">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="38e20-602">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="38e20-602">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="38e20-603">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-603">You can add other formatters for other content types.</span></span>

<span data-ttu-id="38e20-604">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-604">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="38e20-605">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-605">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="38e20-606">組み込みの XML 入力フォーマッタを使用するには: </span><span class="sxs-lookup"><span data-stu-id="38e20-606">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="38e20-607">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="38e20-607">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="38e20-608">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38e20-608">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="38e20-609">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="38e20-609">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="38e20-610">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-610">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="38e20-611">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="38e20-611">Exclude specified types from model binding</span></span>

<span data-ttu-id="38e20-612">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-612">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="38e20-613">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="38e20-613">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="38e20-614">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-614">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="38e20-615">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38e20-615">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="38e20-616">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-616">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="38e20-617">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38e20-617">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="38e20-618">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38e20-618">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="38e20-619">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="38e20-619">Custom model binders</span></span>

<span data-ttu-id="38e20-620">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="38e20-620">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="38e20-621">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38e20-621">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="38e20-622">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="38e20-622">Manual model binding</span></span>

<span data-ttu-id="38e20-623">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="38e20-623">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="38e20-624">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="38e20-624">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="38e20-625">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="38e20-625">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="38e20-626">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-626">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="38e20-627">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38e20-627">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="38e20-628">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="38e20-628">[FromServices] attribute</span></span>

<span data-ttu-id="38e20-629">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="38e20-629">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="38e20-630">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="38e20-630">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="38e20-631">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="38e20-631">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="38e20-632">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="38e20-632">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38e20-633">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="38e20-633">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
