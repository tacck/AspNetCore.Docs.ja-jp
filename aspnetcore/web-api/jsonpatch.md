---
<span data-ttu-id="0917d-101">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-102">'Blazor'</span></span>
- <span data-ttu-id="0917d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-103">'Identity'</span></span>
- <span data-ttu-id="0917d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-105">'Razor'</span></span>
- <span data-ttu-id="0917d-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="0917d-107">ASP.NET Core Web API における Json パッチ</span><span class="sxs-lookup"><span data-stu-id="0917d-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="0917d-108">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="0917d-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0917d-109">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0917d-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0917d-110">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="0917d-110">Package installation</span></span>

<span data-ttu-id="0917d-111">アプリで JSON 修正プログラムのサポートを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="0917d-112">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0917d-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="0917d-113">`Startup.ConfigureServices`を呼び出すようにプロジェクトのメソッドを更新し <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="0917d-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="0917d-115">`AddNewtonsoftJson` は MVC サービス登録メソッドと互換性があります。</span><span class="sxs-lookup"><span data-stu-id="0917d-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="0917d-116">JSON Patch、AddNewtonsoftJson、および system.string</span><span class="sxs-lookup"><span data-stu-id="0917d-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="0917d-117">`AddNewtonsoftJson`すべての `System.Text.Json` JSON コンテンツの書式設定に使用される**all** 、ベースの入力フォーマッタと出力フォーマッタを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0917d-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="0917d-118">を使用して JSON 修正プログラムのサポートを追加するには `Newtonsoft.Json` 、他のフォーマッタを変更せずに、プロジェクトのメソッドを次のように更新し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="0917d-119">上記のコードでは、 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` パッケージと次のステートメントが必要です `using` 。</span><span class="sxs-lookup"><span data-stu-id="0917d-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="0917d-120">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="0917d-120">PATCH HTTP request method</span></span>

<span data-ttu-id="0917d-121">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="0917d-122">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="0917d-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="0917d-123">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0917d-123">JSON Patch</span></span>

<span data-ttu-id="0917d-124">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="0917d-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="0917d-125">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0917d-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="0917d-126">各操作は、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="0917d-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="0917d-127">このような変更の例としては、配列要素の追加やプロパティ値の置換などがあります。</span><span class="sxs-lookup"><span data-stu-id="0917d-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="0917d-128">たとえば、次の JSON ドキュメントはリソース、リソースの JSON パッチドキュメント、およびパッチ操作の適用結果を表します。</span><span class="sxs-lookup"><span data-stu-id="0917d-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="0917d-129">リソースの例</span><span class="sxs-lookup"><span data-stu-id="0917d-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="0917d-130">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="0917d-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="0917d-131">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="0917d-131">In the preceding JSON:</span></span>

* <span data-ttu-id="0917d-132">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="0917d-133">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="0917d-134">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="0917d-135">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="0917d-135">Resource after patch</span></span>

<span data-ttu-id="0917d-136">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="0917d-137">JSON 修正プログラムドキュメントをリソースに適用することによって行われた変更はアトミックです。</span><span class="sxs-lookup"><span data-stu-id="0917d-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="0917d-138">リスト内のいずれかの操作が失敗した場合、リスト内の操作は適用されません。</span><span class="sxs-lookup"><span data-stu-id="0917d-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="0917d-139">パス構文</span><span class="sxs-lookup"><span data-stu-id="0917d-139">Path syntax</span></span>

<span data-ttu-id="0917d-140">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="0917d-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="0917d-141">たとえば、`"/address/zipCode"` となります。</span><span class="sxs-lookup"><span data-stu-id="0917d-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="0917d-142">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="0917d-143">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="0917d-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="0917d-144">`add`配列の末尾には、 `-` インデックス番号ではなくハイフン () を使用し `/addresses/-` ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="0917d-145">オペレーション</span><span class="sxs-lookup"><span data-stu-id="0917d-145">Operations</span></span>

<span data-ttu-id="0917d-146">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="0917d-147">Operation</span><span class="sxs-lookup"><span data-stu-id="0917d-147">Operation</span></span>  | <span data-ttu-id="0917d-148">メモ</span><span class="sxs-lookup"><span data-stu-id="0917d-148">Notes</span></span> |
|---
<span data-ttu-id="0917d-149">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-150">'Blazor'</span></span>
- <span data-ttu-id="0917d-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-151">'Identity'</span></span>
- <span data-ttu-id="0917d-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-153">'Razor'</span></span>
- <span data-ttu-id="0917d-154">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-155">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-156">'Blazor'</span></span>
- <span data-ttu-id="0917d-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-157">'Identity'</span></span>
- <span data-ttu-id="0917d-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-159">'Razor'</span></span>
- <span data-ttu-id="0917d-160">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-161">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-162">'Blazor'</span></span>
- <span data-ttu-id="0917d-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-163">'Identity'</span></span>
- <span data-ttu-id="0917d-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-165">'Razor'</span></span>
- <span data-ttu-id="0917d-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-166">'SignalR' uid:</span></span> 

<span data-ttu-id="0917d-167">------|---
タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-168">'Blazor'</span></span>
- <span data-ttu-id="0917d-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-169">'Identity'</span></span>
- <span data-ttu-id="0917d-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-171">'Razor'</span></span>
- <span data-ttu-id="0917d-172">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-173">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-174">'Blazor'</span></span>
- <span data-ttu-id="0917d-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-175">'Identity'</span></span>
- <span data-ttu-id="0917d-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-177">'Razor'</span></span>
- <span data-ttu-id="0917d-178">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-179">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-180">'Blazor'</span></span>
- <span data-ttu-id="0917d-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-181">'Identity'</span></span>
- <span data-ttu-id="0917d-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-183">'Razor'</span></span>
- <span data-ttu-id="0917d-184">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-185">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-186">'Blazor'</span></span>
- <span data-ttu-id="0917d-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-187">'Identity'</span></span>
- <span data-ttu-id="0917d-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-189">'Razor'</span></span>
- <span data-ttu-id="0917d-190">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-191">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-192">'Blazor'</span></span>
- <span data-ttu-id="0917d-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-193">'Identity'</span></span>
- <span data-ttu-id="0917d-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-195">'Razor'</span></span>
- <span data-ttu-id="0917d-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-197">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-198">'Blazor'</span></span>
- <span data-ttu-id="0917d-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-199">'Identity'</span></span>
- <span data-ttu-id="0917d-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-201">'Razor'</span></span>
- <span data-ttu-id="0917d-202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-203">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-204">'Blazor'</span></span>
- <span data-ttu-id="0917d-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-205">'Identity'</span></span>
- <span data-ttu-id="0917d-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-207">'Razor'</span></span>
- <span data-ttu-id="0917d-208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-209">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-210">'Blazor'</span></span>
- <span data-ttu-id="0917d-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-211">'Identity'</span></span>
- <span data-ttu-id="0917d-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-213">'Razor'</span></span>
- <span data-ttu-id="0917d-214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-215">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-216">'Blazor'</span></span>
- <span data-ttu-id="0917d-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-217">'Identity'</span></span>
- <span data-ttu-id="0917d-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-219">'Razor'</span></span>
- <span data-ttu-id="0917d-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-221">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-222">'Blazor'</span></span>
- <span data-ttu-id="0917d-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-223">'Identity'</span></span>
- <span data-ttu-id="0917d-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-225">'Razor'</span></span>
- <span data-ttu-id="0917d-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-227">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-228">'Blazor'</span></span>
- <span data-ttu-id="0917d-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-229">'Identity'</span></span>
- <span data-ttu-id="0917d-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-231">'Razor'</span></span>
- <span data-ttu-id="0917d-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-233">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-234">'Blazor'</span></span>
- <span data-ttu-id="0917d-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-235">'Identity'</span></span>
- <span data-ttu-id="0917d-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-237">'Razor'</span></span>
- <span data-ttu-id="0917d-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-239">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-240">'Blazor'</span></span>
- <span data-ttu-id="0917d-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-241">'Identity'</span></span>
- <span data-ttu-id="0917d-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-243">'Razor'</span></span>
- <span data-ttu-id="0917d-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-245">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-246">'Blazor'</span></span>
- <span data-ttu-id="0917d-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-247">'Identity'</span></span>
- <span data-ttu-id="0917d-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-249">'Razor'</span></span>
- <span data-ttu-id="0917d-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-250">'SignalR' uid:</span></span> 

<span data-ttu-id="0917d-251">----------------| |`add`     |プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="0917d-252">既存のプロパティの場合: 値を設定します |。|`remove`  |プロパティまたは配列の要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="0917d-253">| |`replace` |の `remove` 後に同じ場所を指定した場合と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="0917d-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="0917d-254">| |`move`    |Source から `remove` の値を使用して、ソースから宛先への変換元と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="0917d-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="0917d-255">| |`copy`    |`add`Source の値を使用して destination と同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="0917d-256">| |`test`    |値が `path` = 指定されている場合 `value` 、成功の状態コードを返します |。</span><span class="sxs-lookup"><span data-stu-id="0917d-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="0917d-257">ASP.NET Core の JSON 修正プログラム</span><span class="sxs-lookup"><span data-stu-id="0917d-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="0917d-258">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="0917d-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="0917d-259">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="0917d-259">Action method code</span></span>

<span data-ttu-id="0917d-260">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0917d-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="0917d-261">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="0917d-262">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0917d-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="0917d-263">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="0917d-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="0917d-264">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="0917d-265">このサンプルアプリのコードは、次のモデルで動作し `Customer` ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="0917d-266">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0917d-266">The sample action method:</span></span>

* <span data-ttu-id="0917d-267">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="0917d-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="0917d-268">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="0917d-268">Applies the patch.</span></span>
* <span data-ttu-id="0917d-269">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="0917d-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="0917d-270">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0917d-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="0917d-271">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="0917d-271">Model state</span></span>

<span data-ttu-id="0917d-272">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="0917d-273">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="0917d-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="0917d-274">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="0917d-275">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0917d-275">Dynamic objects</span></span>

<span data-ttu-id="0917d-276">次のアクションメソッドの例は、修正プログラムを動的オブジェクトに適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="0917d-277">追加の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-277">The add operation</span></span>

* <span data-ttu-id="0917d-278">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="0917d-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="0917d-279">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="0917d-280">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="0917d-281">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="0917d-282">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="0917d-283">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="0917d-284">削除の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-284">The remove operation</span></span>

* <span data-ttu-id="0917d-285">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="0917d-286">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="0917d-287">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="0917d-288">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="0917d-289">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="0917d-290">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="0917d-291">次のサンプルパッチドキュメントでは、 `CustomerName` を null に設定し、を削除し `Orders[0]` ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="0917d-292">置換の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-292">The replace operation</span></span>

<span data-ttu-id="0917d-293">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="0917d-294">次のサンプルパッチドキュメントでは、の値をに設定 `CustomerName` し、 `Orders[0]` 新しいオブジェクトで置き換え `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="0917d-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="0917d-295">移動の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-295">The move operation</span></span>

* <span data-ttu-id="0917d-296">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="0917d-297">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="0917d-298">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="0917d-299">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="0917d-300">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="0917d-301">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0917d-301">The following sample patch document:</span></span>

* <span data-ttu-id="0917d-302">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0917d-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0917d-303">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="0917d-304">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="0917d-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="0917d-305">コピー操作</span><span class="sxs-lookup"><span data-stu-id="0917d-305">The copy operation</span></span>

<span data-ttu-id="0917d-306">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="0917d-307">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0917d-307">The following sample patch document:</span></span>

* <span data-ttu-id="0917d-308">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0917d-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0917d-309">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="0917d-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="0917d-310">テストの操作</span><span class="sxs-lookup"><span data-stu-id="0917d-310">The test operation</span></span>

<span data-ttu-id="0917d-311">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="0917d-312">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="0917d-313">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="0917d-314">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="0917d-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="0917d-315">コードを入手する</span><span class="sxs-lookup"><span data-stu-id="0917d-315">Get the code</span></span>

<span data-ttu-id="0917d-316">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)します。</span><span class="sxs-lookup"><span data-stu-id="0917d-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="0917d-317">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0917d-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0917d-318">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0917d-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="0917d-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="0917d-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="0917d-320">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="0917d-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="0917d-321">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="0917d-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="0917d-322">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0917d-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0917d-323">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="0917d-323">Additional resources</span></span>

* [<span data-ttu-id="0917d-324">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="0917d-325">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="0917d-326">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="0917d-327">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="0917d-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="0917d-328">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="0917d-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="0917d-329">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="0917d-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0917d-330">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0917d-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="0917d-331">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="0917d-331">PATCH HTTP request method</span></span>

<span data-ttu-id="0917d-332">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="0917d-333">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="0917d-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="0917d-334">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0917d-334">JSON Patch</span></span>

<span data-ttu-id="0917d-335">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="0917d-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="0917d-336">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0917d-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="0917d-337">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="0917d-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="0917d-338">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="0917d-339">リソースの例</span><span class="sxs-lookup"><span data-stu-id="0917d-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="0917d-340">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="0917d-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="0917d-341">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="0917d-341">In the preceding JSON:</span></span>

* <span data-ttu-id="0917d-342">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="0917d-343">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="0917d-344">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="0917d-345">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="0917d-345">Resource after patch</span></span>

<span data-ttu-id="0917d-346">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="0917d-347">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="0917d-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="0917d-348">パス構文</span><span class="sxs-lookup"><span data-stu-id="0917d-348">Path syntax</span></span>

<span data-ttu-id="0917d-349">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="0917d-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="0917d-350">たとえば、`"/address/zipCode"` となります。</span><span class="sxs-lookup"><span data-stu-id="0917d-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="0917d-351">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="0917d-352">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="0917d-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="0917d-353">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0917d-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="0917d-354">オペレーション</span><span class="sxs-lookup"><span data-stu-id="0917d-354">Operations</span></span>

<span data-ttu-id="0917d-355">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="0917d-356">Operation</span><span class="sxs-lookup"><span data-stu-id="0917d-356">Operation</span></span>  | <span data-ttu-id="0917d-357">メモ</span><span class="sxs-lookup"><span data-stu-id="0917d-357">Notes</span></span> |
|---
<span data-ttu-id="0917d-358">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-359">'Blazor'</span></span>
- <span data-ttu-id="0917d-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-360">'Identity'</span></span>
- <span data-ttu-id="0917d-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-362">'Razor'</span></span>
- <span data-ttu-id="0917d-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-364">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-365">'Blazor'</span></span>
- <span data-ttu-id="0917d-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-366">'Identity'</span></span>
- <span data-ttu-id="0917d-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-368">'Razor'</span></span>
- <span data-ttu-id="0917d-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-370">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-371">'Blazor'</span></span>
- <span data-ttu-id="0917d-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-372">'Identity'</span></span>
- <span data-ttu-id="0917d-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-374">'Razor'</span></span>
- <span data-ttu-id="0917d-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-375">'SignalR' uid:</span></span> 

<span data-ttu-id="0917d-376">------|---
タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-377">'Blazor'</span></span>
- <span data-ttu-id="0917d-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-378">'Identity'</span></span>
- <span data-ttu-id="0917d-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-380">'Razor'</span></span>
- <span data-ttu-id="0917d-381">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-382">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-383">'Blazor'</span></span>
- <span data-ttu-id="0917d-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-384">'Identity'</span></span>
- <span data-ttu-id="0917d-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-386">'Razor'</span></span>
- <span data-ttu-id="0917d-387">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-388">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-389">'Blazor'</span></span>
- <span data-ttu-id="0917d-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-390">'Identity'</span></span>
- <span data-ttu-id="0917d-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-392">'Razor'</span></span>
- <span data-ttu-id="0917d-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-394">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-395">'Blazor'</span></span>
- <span data-ttu-id="0917d-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-396">'Identity'</span></span>
- <span data-ttu-id="0917d-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-398">'Razor'</span></span>
- <span data-ttu-id="0917d-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-400">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-401">'Blazor'</span></span>
- <span data-ttu-id="0917d-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-402">'Identity'</span></span>
- <span data-ttu-id="0917d-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-404">'Razor'</span></span>
- <span data-ttu-id="0917d-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-406">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-407">'Blazor'</span></span>
- <span data-ttu-id="0917d-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-408">'Identity'</span></span>
- <span data-ttu-id="0917d-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-410">'Razor'</span></span>
- <span data-ttu-id="0917d-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-412">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-413">'Blazor'</span></span>
- <span data-ttu-id="0917d-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-414">'Identity'</span></span>
- <span data-ttu-id="0917d-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-416">'Razor'</span></span>
- <span data-ttu-id="0917d-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-418">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-419">'Blazor'</span></span>
- <span data-ttu-id="0917d-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-420">'Identity'</span></span>
- <span data-ttu-id="0917d-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-422">'Razor'</span></span>
- <span data-ttu-id="0917d-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-424">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-425">'Blazor'</span></span>
- <span data-ttu-id="0917d-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-426">'Identity'</span></span>
- <span data-ttu-id="0917d-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-428">'Razor'</span></span>
- <span data-ttu-id="0917d-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-430">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-431">'Blazor'</span></span>
- <span data-ttu-id="0917d-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-432">'Identity'</span></span>
- <span data-ttu-id="0917d-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-434">'Razor'</span></span>
- <span data-ttu-id="0917d-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-436">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-437">'Blazor'</span></span>
- <span data-ttu-id="0917d-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-438">'Identity'</span></span>
- <span data-ttu-id="0917d-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-440">'Razor'</span></span>
- <span data-ttu-id="0917d-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-442">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-443">'Blazor'</span></span>
- <span data-ttu-id="0917d-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-444">'Identity'</span></span>
- <span data-ttu-id="0917d-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-446">'Razor'</span></span>
- <span data-ttu-id="0917d-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-448">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-449">'Blazor'</span></span>
- <span data-ttu-id="0917d-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-450">'Identity'</span></span>
- <span data-ttu-id="0917d-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-452">'Razor'</span></span>
- <span data-ttu-id="0917d-453">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0917d-454">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="0917d-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0917d-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0917d-455">'Blazor'</span></span>
- <span data-ttu-id="0917d-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0917d-456">'Identity'</span></span>
- <span data-ttu-id="0917d-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0917d-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="0917d-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0917d-458">'Razor'</span></span>
- <span data-ttu-id="0917d-459">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0917d-459">'SignalR' uid:</span></span> 

<span data-ttu-id="0917d-460">----------------| |`add`     |プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="0917d-461">既存のプロパティの場合: 値を設定します |。|`remove`  |プロパティまたは配列の要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="0917d-462">| |`replace` |の `remove` 後に同じ場所を指定した場合と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="0917d-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="0917d-463">| |`move`    |Source から `remove` の値を使用して、ソースから宛先への変換元と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="0917d-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="0917d-464">| |`copy`    |`add`Source の値を使用して destination と同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="0917d-465">| |`test`    |値が `path` = 指定されている場合 `value` 、成功の状態コードを返します |。</span><span class="sxs-lookup"><span data-stu-id="0917d-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="0917d-466">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="0917d-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="0917d-467">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="0917d-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="0917d-468">パッケージは、 [AspnetCore](xref:fundamentals/metapackage-app)メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="0917d-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="0917d-469">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="0917d-469">Action method code</span></span>

<span data-ttu-id="0917d-470">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0917d-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="0917d-471">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="0917d-472">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0917d-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="0917d-473">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="0917d-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="0917d-474">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="0917d-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="0917d-475">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="0917d-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="0917d-476">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="0917d-476">The sample action method:</span></span>

* <span data-ttu-id="0917d-477">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="0917d-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="0917d-478">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="0917d-478">Applies the patch.</span></span>
* <span data-ttu-id="0917d-479">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="0917d-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="0917d-480">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="0917d-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="0917d-481">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="0917d-481">Model state</span></span>

<span data-ttu-id="0917d-482">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="0917d-483">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="0917d-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="0917d-484">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="0917d-485">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0917d-485">Dynamic objects</span></span>

<span data-ttu-id="0917d-486">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0917d-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="0917d-487">追加の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-487">The add operation</span></span>

* <span data-ttu-id="0917d-488">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="0917d-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="0917d-489">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="0917d-490">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="0917d-491">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="0917d-492">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="0917d-493">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="0917d-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="0917d-494">削除の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-494">The remove operation</span></span>

* <span data-ttu-id="0917d-495">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="0917d-496">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="0917d-497">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="0917d-498">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="0917d-499">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="0917d-500">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="0917d-501">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="0917d-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="0917d-502">置換の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-502">The replace operation</span></span>

<span data-ttu-id="0917d-503">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="0917d-504">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0917d-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="0917d-505">移動の操作</span><span class="sxs-lookup"><span data-stu-id="0917d-505">The move operation</span></span>

* <span data-ttu-id="0917d-506">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="0917d-507">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="0917d-508">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="0917d-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="0917d-509">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="0917d-510">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="0917d-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="0917d-511">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0917d-511">The following sample patch document:</span></span>

* <span data-ttu-id="0917d-512">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0917d-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0917d-513">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="0917d-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="0917d-514">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="0917d-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="0917d-515">コピー操作</span><span class="sxs-lookup"><span data-stu-id="0917d-515">The copy operation</span></span>

<span data-ttu-id="0917d-516">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="0917d-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="0917d-517">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0917d-517">The following sample patch document:</span></span>

* <span data-ttu-id="0917d-518">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="0917d-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="0917d-519">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="0917d-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="0917d-520">テストの操作</span><span class="sxs-lookup"><span data-stu-id="0917d-520">The test operation</span></span>

<span data-ttu-id="0917d-521">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="0917d-522">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="0917d-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="0917d-523">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0917d-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="0917d-524">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="0917d-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="0917d-525">コードを入手する</span><span class="sxs-lookup"><span data-stu-id="0917d-525">Get the code</span></span>

<span data-ttu-id="0917d-526">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="0917d-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="0917d-527">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0917d-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0917d-528">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0917d-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="0917d-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="0917d-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="0917d-530">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="0917d-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="0917d-531">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="0917d-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="0917d-532">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0917d-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0917d-533">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="0917d-533">Additional resources</span></span>

* [<span data-ttu-id="0917d-534">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="0917d-535">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="0917d-536">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="0917d-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="0917d-537">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="0917d-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="0917d-538">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="0917d-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="0917d-539">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="0917d-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
