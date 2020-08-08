---
title: ASP.NET Core Web API における Json パッチ
author: rick-anderson
description: ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 09da557d678889ba16abe6f9af40ae1b33583d8b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022369"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="0610f-103">ASP.NET Core Web API における Json パッチ</span><span class="sxs-lookup"><span data-stu-id="0610f-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="0610f-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="0610f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0610f-105">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0610f-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0610f-106">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="0610f-106">Package installation</span></span>

<span data-ttu-id="0610f-107">アプリで JSON 修正プログラムのサポートを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="0610f-108">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0610f-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="0610f-109">`Startup.ConfigureServices`を呼び出すようにプロジェクトのメソッドを更新し <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="0610f-110">例:</span><span class="sxs-lookup"><span data-stu-id="0610f-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="0610f-111">`AddNewtonsoftJson` は MVC サービス登録メソッドと互換性があります。</span><span class="sxs-lookup"><span data-stu-id="0610f-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="0610f-112">JSON 修正プログラム、AddNewtonsoftJson、および System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="0610f-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="0610f-113">`AddNewtonsoftJson`すべての `System.Text.Json` JSON コンテンツの書式設定に使用される**all** 、ベースの入力フォーマッタと出力フォーマッタを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0610f-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="0610f-114">を使用して JSON 修正プログラムのサポートを追加するには `Newtonsoft.Json` 、他のフォーマッタを変更せずに、プロジェクトのメソッドを次のように更新し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="0610f-115">上記のコードでは、 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` パッケージと次のステートメントが必要です `using` 。</span><span class="sxs-lookup"><span data-stu-id="0610f-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="0610f-116">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="0610f-116">PATCH HTTP request method</span></span>

<span data-ttu-id="0610f-117">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="0610f-118">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="0610f-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="0610f-119">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0610f-119">JSON Patch</span></span>

<span data-ttu-id="0610f-120">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="0610f-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="0610f-121">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0610f-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="0610f-122">各操作は、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="0610f-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="0610f-123">このような変更の例としては、配列要素の追加やプロパティ値の置換などがあります。</span><span class="sxs-lookup"><span data-stu-id="0610f-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="0610f-124">たとえば、次の JSON ドキュメントはリソース、リソースの JSON パッチドキュメント、およびパッチ操作の適用結果を表します。</span><span class="sxs-lookup"><span data-stu-id="0610f-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="0610f-125">リソースの例</span><span class="sxs-lookup"><span data-stu-id="0610f-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="0610f-126">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="0610f-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="0610f-127">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="0610f-127">In the preceding JSON:</span></span>

* <span data-ttu-id="0610f-128">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="0610f-129">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="0610f-130">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="0610f-131">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="0610f-131">Resource after patch</span></span>

<span data-ttu-id="0610f-132">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="0610f-133">JSON 修正プログラムドキュメントをリソースに適用することによって行われた変更はアトミックです。</span><span class="sxs-lookup"><span data-stu-id="0610f-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="0610f-134">リスト内のいずれかの操作が失敗した場合、リスト内の操作は適用されません。</span><span class="sxs-lookup"><span data-stu-id="0610f-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="0610f-135">パス構文</span><span class="sxs-lookup"><span data-stu-id="0610f-135">Path syntax</span></span>

<span data-ttu-id="0610f-136">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="0610f-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="0610f-137">たとえば、`"/address/zipCode"` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="0610f-138">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="0610f-139">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="0610f-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="0610f-140">`add`配列の末尾には、 `-` インデックス番号ではなくハイフン () を使用し `/addresses/-` ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="0610f-141">操作</span><span class="sxs-lookup"><span data-stu-id="0610f-141">Operations</span></span>

<span data-ttu-id="0610f-142">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="0610f-143">操作</span><span class="sxs-lookup"><span data-stu-id="0610f-143">Operation</span></span>  | <span data-ttu-id="0610f-144">メモ</span><span class="sxs-lookup"><span data-stu-id="0610f-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="0610f-145">プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-145">Add a property or array element.</span></span> <span data-ttu-id="0610f-146">既存のプロパティの場合: 値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="0610f-147">プロパティまたは配列要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="0610f-148">`remove` の後に、同じ場所で `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="0610f-149">ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="0610f-150">ソースからの値を使用した宛先への `add` と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="0610f-151">`path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="0610f-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="0610f-152">ASP.NET Core の JSON 修正プログラム</span><span class="sxs-lookup"><span data-stu-id="0610f-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="0610f-153">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="0610f-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="0610f-154">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="0610f-154">Action method code</span></span>

<span data-ttu-id="0610f-155">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0610f-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="0610f-156">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="0610f-157">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0610f-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="0610f-158">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="0610f-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="0610f-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="0610f-160">このサンプルアプリのコードは、次のモデルで動作し `Customer` ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="0610f-161">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0610f-161">The sample action method:</span></span>

* <span data-ttu-id="0610f-162">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="0610f-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="0610f-163">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="0610f-163">Applies the patch.</span></span>
* <span data-ttu-id="0610f-164">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="0610f-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="0610f-165">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0610f-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="0610f-166">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="0610f-166">Model state</span></span>

<span data-ttu-id="0610f-167">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="0610f-168">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="0610f-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="0610f-169">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="0610f-170">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0610f-170">Dynamic objects</span></span>

<span data-ttu-id="0610f-171">次のアクションメソッドの例は、修正プログラムを動的オブジェクトに適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="0610f-172">追加の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-172">The add operation</span></span>

* <span data-ttu-id="0610f-173">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="0610f-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="0610f-174">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="0610f-175">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="0610f-176">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="0610f-177">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="0610f-178">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="0610f-179">削除の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-179">The remove operation</span></span>

* <span data-ttu-id="0610f-180">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="0610f-181">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="0610f-182">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="0610f-183">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="0610f-184">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="0610f-185">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="0610f-186">次のサンプルパッチドキュメントでは、 `CustomerName` を null に設定し、を削除し `Orders[0]` ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="0610f-187">置換の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-187">The replace operation</span></span>

<span data-ttu-id="0610f-188">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="0610f-189">次のサンプルパッチドキュメントでは、の値をに設定 `CustomerName` し、 `Orders[0]` 新しいオブジェクトで置き換え `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="0610f-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="0610f-190">移動の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-190">The move operation</span></span>

* <span data-ttu-id="0610f-191">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="0610f-192">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="0610f-193">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="0610f-194">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="0610f-195">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="0610f-196">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0610f-196">The following sample patch document:</span></span>

* <span data-ttu-id="0610f-197">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0610f-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0610f-198">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="0610f-199">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="0610f-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="0610f-200">コピー操作</span><span class="sxs-lookup"><span data-stu-id="0610f-200">The copy operation</span></span>

<span data-ttu-id="0610f-201">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="0610f-202">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0610f-202">The following sample patch document:</span></span>

* <span data-ttu-id="0610f-203">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0610f-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0610f-204">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="0610f-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="0610f-205">テストの操作</span><span class="sxs-lookup"><span data-stu-id="0610f-205">The test operation</span></span>

<span data-ttu-id="0610f-206">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="0610f-207">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="0610f-208">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="0610f-209">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="0610f-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="0610f-210">コードの入手</span><span class="sxs-lookup"><span data-stu-id="0610f-210">Get the code</span></span>

<span data-ttu-id="0610f-211">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)します。</span><span class="sxs-lookup"><span data-stu-id="0610f-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="0610f-212">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0610f-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0610f-213">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0610f-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="0610f-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="0610f-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="0610f-215">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="0610f-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="0610f-216">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="0610f-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="0610f-217">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0610f-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0610f-218">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="0610f-218">Additional resources</span></span>

* [<span data-ttu-id="0610f-219">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="0610f-220">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="0610f-221">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="0610f-222">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="0610f-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="0610f-223">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="0610f-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="0610f-224">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="0610f-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0610f-225">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0610f-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="0610f-226">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="0610f-226">PATCH HTTP request method</span></span>

<span data-ttu-id="0610f-227">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="0610f-228">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="0610f-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="0610f-229">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0610f-229">JSON Patch</span></span>

<span data-ttu-id="0610f-230">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="0610f-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="0610f-231">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0610f-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="0610f-232">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="0610f-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="0610f-233">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="0610f-234">リソースの例</span><span class="sxs-lookup"><span data-stu-id="0610f-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="0610f-235">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="0610f-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="0610f-236">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="0610f-236">In the preceding JSON:</span></span>

* <span data-ttu-id="0610f-237">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="0610f-238">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="0610f-239">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="0610f-240">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="0610f-240">Resource after patch</span></span>

<span data-ttu-id="0610f-241">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="0610f-242">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="0610f-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="0610f-243">パス構文</span><span class="sxs-lookup"><span data-stu-id="0610f-243">Path syntax</span></span>

<span data-ttu-id="0610f-244">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="0610f-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="0610f-245">たとえば、`"/address/zipCode"` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="0610f-246">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="0610f-247">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="0610f-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="0610f-248">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0610f-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="0610f-249">操作</span><span class="sxs-lookup"><span data-stu-id="0610f-249">Operations</span></span>

<span data-ttu-id="0610f-250">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="0610f-251">操作</span><span class="sxs-lookup"><span data-stu-id="0610f-251">Operation</span></span>  | <span data-ttu-id="0610f-252">メモ</span><span class="sxs-lookup"><span data-stu-id="0610f-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="0610f-253">プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-253">Add a property or array element.</span></span> <span data-ttu-id="0610f-254">既存のプロパティの場合: 値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="0610f-255">プロパティまたは配列要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="0610f-256">`remove` の後に、同じ場所で `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="0610f-257">ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="0610f-258">ソースからの値を使用した宛先への `add` と同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="0610f-259">`path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="0610f-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="0610f-260">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0610f-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="0610f-261">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="0610f-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="0610f-262">パッケージは、 [AspnetCore](xref:fundamentals/metapackage-app)メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0610f-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="0610f-263">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="0610f-263">Action method code</span></span>

<span data-ttu-id="0610f-264">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0610f-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="0610f-265">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="0610f-266">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0610f-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="0610f-267">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="0610f-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="0610f-268">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0610f-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="0610f-269">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="0610f-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="0610f-270">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0610f-270">The sample action method:</span></span>

* <span data-ttu-id="0610f-271">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="0610f-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="0610f-272">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="0610f-272">Applies the patch.</span></span>
* <span data-ttu-id="0610f-273">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="0610f-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="0610f-274">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0610f-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="0610f-275">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="0610f-275">Model state</span></span>

<span data-ttu-id="0610f-276">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="0610f-277">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="0610f-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="0610f-278">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="0610f-279">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0610f-279">Dynamic objects</span></span>

<span data-ttu-id="0610f-280">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0610f-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="0610f-281">追加の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-281">The add operation</span></span>

* <span data-ttu-id="0610f-282">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="0610f-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="0610f-283">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="0610f-284">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="0610f-285">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="0610f-286">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="0610f-287">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="0610f-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="0610f-288">削除の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-288">The remove operation</span></span>

* <span data-ttu-id="0610f-289">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="0610f-290">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="0610f-291">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="0610f-292">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="0610f-293">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="0610f-294">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="0610f-295">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="0610f-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="0610f-296">置換の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-296">The replace operation</span></span>

<span data-ttu-id="0610f-297">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="0610f-298">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0610f-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="0610f-299">移動の操作</span><span class="sxs-lookup"><span data-stu-id="0610f-299">The move operation</span></span>

* <span data-ttu-id="0610f-300">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="0610f-301">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="0610f-302">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0610f-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="0610f-303">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="0610f-304">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0610f-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="0610f-305">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0610f-305">The following sample patch document:</span></span>

* <span data-ttu-id="0610f-306">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0610f-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0610f-307">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0610f-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="0610f-308">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="0610f-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="0610f-309">コピー操作</span><span class="sxs-lookup"><span data-stu-id="0610f-309">The copy operation</span></span>

<span data-ttu-id="0610f-310">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0610f-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="0610f-311">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0610f-311">The following sample patch document:</span></span>

* <span data-ttu-id="0610f-312">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0610f-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0610f-313">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="0610f-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="0610f-314">テストの操作</span><span class="sxs-lookup"><span data-stu-id="0610f-314">The test operation</span></span>

<span data-ttu-id="0610f-315">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="0610f-316">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="0610f-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="0610f-317">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0610f-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="0610f-318">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="0610f-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="0610f-319">コードの入手</span><span class="sxs-lookup"><span data-stu-id="0610f-319">Get the code</span></span>

<span data-ttu-id="0610f-320">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="0610f-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="0610f-321">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0610f-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0610f-322">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0610f-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="0610f-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="0610f-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="0610f-324">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="0610f-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="0610f-325">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="0610f-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="0610f-326">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0610f-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0610f-327">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="0610f-327">Additional resources</span></span>

* [<span data-ttu-id="0610f-328">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="0610f-329">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="0610f-330">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="0610f-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="0610f-331">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="0610f-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="0610f-332">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="0610f-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="0610f-333">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="0610f-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
