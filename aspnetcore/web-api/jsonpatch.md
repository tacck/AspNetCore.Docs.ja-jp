---
<span data-ttu-id="1776b-101">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-102">'Blazor'</span></span>
- <span data-ttu-id="1776b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-103">'Identity'</span></span>
- <span data-ttu-id="1776b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-105">'Razor'</span></span>
- <span data-ttu-id="1776b-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="1776b-107">ASP.NET Core Web API における Json パッチ</span><span class="sxs-lookup"><span data-stu-id="1776b-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="1776b-108">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="1776b-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1776b-109">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1776b-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="1776b-110">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="1776b-110">Package installation</span></span>

<span data-ttu-id="1776b-111">アプリで JSON 修正プログラムのサポートを有効にするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="1776b-112">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1776b-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="1776b-113">`Startup.ConfigureServices`を呼び出すようにプロジェクトのメソッドを更新し <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="1776b-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="1776b-115">`AddNewtonsoftJson` は MVC サービス登録メソッドと互換性があります。</span><span class="sxs-lookup"><span data-stu-id="1776b-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="1776b-116">JSON Patch、AddNewtonsoftJson、および system.string</span><span class="sxs-lookup"><span data-stu-id="1776b-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="1776b-117">`AddNewtonsoftJson`すべての `System.Text.Json` JSON コンテンツの書式設定に使用される**all** 、ベースの入力フォーマッタと出力フォーマッタを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1776b-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="1776b-118">を使用して JSON 修正プログラムのサポートを追加するには `Newtonsoft.Json` 、他のフォーマッタを変更せずに、プロジェクトのメソッドを次のように更新し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="1776b-119">上記のコードでは、 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` パッケージと次のステートメントが必要です `using` 。</span><span class="sxs-lookup"><span data-stu-id="1776b-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="1776b-120">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="1776b-120">PATCH HTTP request method</span></span>

<span data-ttu-id="1776b-121">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="1776b-122">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="1776b-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="1776b-123">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="1776b-123">JSON Patch</span></span>

<span data-ttu-id="1776b-124">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="1776b-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="1776b-125">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1776b-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="1776b-126">各操作は、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="1776b-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="1776b-127">このような変更の例としては、配列要素の追加やプロパティ値の置換などがあります。</span><span class="sxs-lookup"><span data-stu-id="1776b-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="1776b-128">たとえば、次の JSON ドキュメントはリソース、リソースの JSON パッチドキュメント、およびパッチ操作の適用結果を表します。</span><span class="sxs-lookup"><span data-stu-id="1776b-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="1776b-129">リソースの例</span><span class="sxs-lookup"><span data-stu-id="1776b-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="1776b-130">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="1776b-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="1776b-131">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="1776b-131">In the preceding JSON:</span></span>

* <span data-ttu-id="1776b-132">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="1776b-133">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="1776b-134">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="1776b-135">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="1776b-135">Resource after patch</span></span>

<span data-ttu-id="1776b-136">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="1776b-137">JSON 修正プログラムドキュメントをリソースに適用することによって行われた変更はアトミックです。</span><span class="sxs-lookup"><span data-stu-id="1776b-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="1776b-138">リスト内のいずれかの操作が失敗した場合、リスト内の操作は適用されません。</span><span class="sxs-lookup"><span data-stu-id="1776b-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="1776b-139">パス構文</span><span class="sxs-lookup"><span data-stu-id="1776b-139">Path syntax</span></span>

<span data-ttu-id="1776b-140">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="1776b-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="1776b-141">たとえば、「 `"/address/zipCode"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="1776b-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="1776b-142">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="1776b-143">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="1776b-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="1776b-144">`add`配列の末尾には、 `-` インデックス番号ではなくハイフン () を使用し `/addresses/-` ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="1776b-145">オペレーション</span><span class="sxs-lookup"><span data-stu-id="1776b-145">Operations</span></span>

<span data-ttu-id="1776b-146">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="1776b-147">Operation</span><span class="sxs-lookup"><span data-stu-id="1776b-147">Operation</span></span>  | <span data-ttu-id="1776b-148">Notes</span><span class="sxs-lookup"><span data-stu-id="1776b-148">Notes</span></span> |
|---
<span data-ttu-id="1776b-149">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-150">'Blazor'</span></span>
- <span data-ttu-id="1776b-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-151">'Identity'</span></span>
- <span data-ttu-id="1776b-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-153">'Razor'</span></span>
- <span data-ttu-id="1776b-154">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-155">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-156">'Blazor'</span></span>
- <span data-ttu-id="1776b-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-157">'Identity'</span></span>
- <span data-ttu-id="1776b-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-159">'Razor'</span></span>
- <span data-ttu-id="1776b-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-161">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-162">'Blazor'</span></span>
- <span data-ttu-id="1776b-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-163">'Identity'</span></span>
- <span data-ttu-id="1776b-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-165">'Razor'</span></span>
- <span data-ttu-id="1776b-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-166">'SignalR' uid:</span></span> 

<span data-ttu-id="1776b-167">------|---
タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-168">'Blazor'</span></span>
- <span data-ttu-id="1776b-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-169">'Identity'</span></span>
- <span data-ttu-id="1776b-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-171">'Razor'</span></span>
- <span data-ttu-id="1776b-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-173">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-174">'Blazor'</span></span>
- <span data-ttu-id="1776b-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-175">'Identity'</span></span>
- <span data-ttu-id="1776b-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-177">'Razor'</span></span>
- <span data-ttu-id="1776b-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-179">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-180">'Blazor'</span></span>
- <span data-ttu-id="1776b-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-181">'Identity'</span></span>
- <span data-ttu-id="1776b-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-183">'Razor'</span></span>
- <span data-ttu-id="1776b-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-185">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-186">'Blazor'</span></span>
- <span data-ttu-id="1776b-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-187">'Identity'</span></span>
- <span data-ttu-id="1776b-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-189">'Razor'</span></span>
- <span data-ttu-id="1776b-190">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-191">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-192">'Blazor'</span></span>
- <span data-ttu-id="1776b-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-193">'Identity'</span></span>
- <span data-ttu-id="1776b-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-195">'Razor'</span></span>
- <span data-ttu-id="1776b-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-197">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-198">'Blazor'</span></span>
- <span data-ttu-id="1776b-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-199">'Identity'</span></span>
- <span data-ttu-id="1776b-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-201">'Razor'</span></span>
- <span data-ttu-id="1776b-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-203">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-204">'Blazor'</span></span>
- <span data-ttu-id="1776b-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-205">'Identity'</span></span>
- <span data-ttu-id="1776b-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-207">'Razor'</span></span>
- <span data-ttu-id="1776b-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-209">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-210">'Blazor'</span></span>
- <span data-ttu-id="1776b-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-211">'Identity'</span></span>
- <span data-ttu-id="1776b-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-213">'Razor'</span></span>
- <span data-ttu-id="1776b-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-215">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-216">'Blazor'</span></span>
- <span data-ttu-id="1776b-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-217">'Identity'</span></span>
- <span data-ttu-id="1776b-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-219">'Razor'</span></span>
- <span data-ttu-id="1776b-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-221">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-222">'Blazor'</span></span>
- <span data-ttu-id="1776b-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-223">'Identity'</span></span>
- <span data-ttu-id="1776b-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-225">'Razor'</span></span>
- <span data-ttu-id="1776b-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-227">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-228">'Blazor'</span></span>
- <span data-ttu-id="1776b-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-229">'Identity'</span></span>
- <span data-ttu-id="1776b-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-231">'Razor'</span></span>
- <span data-ttu-id="1776b-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-233">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-234">'Blazor'</span></span>
- <span data-ttu-id="1776b-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-235">'Identity'</span></span>
- <span data-ttu-id="1776b-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-237">'Razor'</span></span>
- <span data-ttu-id="1776b-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-239">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-240">'Blazor'</span></span>
- <span data-ttu-id="1776b-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-241">'Identity'</span></span>
- <span data-ttu-id="1776b-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-243">'Razor'</span></span>
- <span data-ttu-id="1776b-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-245">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-246">'Blazor'</span></span>
- <span data-ttu-id="1776b-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-247">'Identity'</span></span>
- <span data-ttu-id="1776b-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-249">'Razor'</span></span>
- <span data-ttu-id="1776b-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-250">'SignalR' uid:</span></span> 

<span data-ttu-id="1776b-251">----------------| |`add`     |プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="1776b-252">既存のプロパティの場合: 値を設定します |。|`remove`  |プロパティまたは配列の要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="1776b-253">| |`replace` |の `remove` 後に同じ場所を指定した場合と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="1776b-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="1776b-254">| |`move`    |Source から `remove` の値を使用して、ソースから宛先への変換元と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="1776b-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="1776b-255">| |`copy`    |`add`Source の値を使用して destination と同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="1776b-256">| |`test`    |値が `path` = 指定されている場合 `value` 、成功の状態コードを返します |。</span><span class="sxs-lookup"><span data-stu-id="1776b-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="1776b-257">ASP.NET Core の JSON 修正プログラム</span><span class="sxs-lookup"><span data-stu-id="1776b-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="1776b-258">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="1776b-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="1776b-259">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="1776b-259">Action method code</span></span>

<span data-ttu-id="1776b-260">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="1776b-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="1776b-261">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="1776b-262">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="1776b-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="1776b-263">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="1776b-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="1776b-264">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="1776b-265">このサンプルアプリのコードは、次のモデルで動作し `Customer` ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="1776b-266">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="1776b-266">The sample action method:</span></span>

* <span data-ttu-id="1776b-267">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="1776b-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="1776b-268">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="1776b-268">Applies the patch.</span></span>
* <span data-ttu-id="1776b-269">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="1776b-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="1776b-270">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="1776b-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="1776b-271">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="1776b-271">Model state</span></span>

<span data-ttu-id="1776b-272">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="1776b-273">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="1776b-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="1776b-274">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="1776b-275">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="1776b-275">Dynamic objects</span></span>

<span data-ttu-id="1776b-276">次のアクションメソッドの例は、修正プログラムを動的オブジェクトに適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="1776b-277">追加の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-277">The add operation</span></span>

* <span data-ttu-id="1776b-278">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1776b-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="1776b-279">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="1776b-280">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="1776b-281">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="1776b-282">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="1776b-283">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="1776b-284">削除の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-284">The remove operation</span></span>

* <span data-ttu-id="1776b-285">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="1776b-286">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="1776b-287">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="1776b-288">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="1776b-289">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="1776b-290">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="1776b-291">次のサンプルパッチドキュメントでは、 `CustomerName` を null に設定し、を削除し `Orders[0]` ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="1776b-292">置換の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-292">The replace operation</span></span>

<span data-ttu-id="1776b-293">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="1776b-294">次のサンプルパッチドキュメントでは、の値をに設定 `CustomerName` し、 `Orders[0]` 新しいオブジェクトで置き換え `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="1776b-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="1776b-295">移動の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-295">The move operation</span></span>

* <span data-ttu-id="1776b-296">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="1776b-297">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="1776b-298">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="1776b-299">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="1776b-300">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="1776b-301">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="1776b-301">The following sample patch document:</span></span>

* <span data-ttu-id="1776b-302">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="1776b-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="1776b-303">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="1776b-304">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="1776b-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="1776b-305">コピー操作</span><span class="sxs-lookup"><span data-stu-id="1776b-305">The copy operation</span></span>

<span data-ttu-id="1776b-306">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="1776b-307">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="1776b-307">The following sample patch document:</span></span>

* <span data-ttu-id="1776b-308">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="1776b-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="1776b-309">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="1776b-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="1776b-310">テストの操作</span><span class="sxs-lookup"><span data-stu-id="1776b-310">The test operation</span></span>

<span data-ttu-id="1776b-311">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="1776b-312">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="1776b-313">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="1776b-314">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="1776b-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="1776b-315">コードを入手する</span><span class="sxs-lookup"><span data-stu-id="1776b-315">Get the code</span></span>

<span data-ttu-id="1776b-316">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)します。</span><span class="sxs-lookup"><span data-stu-id="1776b-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="1776b-317">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1776b-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1776b-318">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="1776b-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="1776b-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="1776b-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="1776b-320">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="1776b-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="1776b-321">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="1776b-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="1776b-322">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="1776b-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1776b-323">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1776b-323">Additional resources</span></span>

* [<span data-ttu-id="1776b-324">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="1776b-325">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="1776b-326">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="1776b-327">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="1776b-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="1776b-328">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="1776b-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="1776b-329">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="1776b-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1776b-330">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1776b-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="1776b-331">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="1776b-331">PATCH HTTP request method</span></span>

<span data-ttu-id="1776b-332">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="1776b-333">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="1776b-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="1776b-334">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="1776b-334">JSON Patch</span></span>

<span data-ttu-id="1776b-335">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="1776b-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="1776b-336">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1776b-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="1776b-337">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="1776b-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="1776b-338">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="1776b-339">リソースの例</span><span class="sxs-lookup"><span data-stu-id="1776b-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="1776b-340">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="1776b-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="1776b-341">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="1776b-341">In the preceding JSON:</span></span>

* <span data-ttu-id="1776b-342">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="1776b-343">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="1776b-344">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="1776b-345">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="1776b-345">Resource after patch</span></span>

<span data-ttu-id="1776b-346">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="1776b-347">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="1776b-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="1776b-348">パス構文</span><span class="sxs-lookup"><span data-stu-id="1776b-348">Path syntax</span></span>

<span data-ttu-id="1776b-349">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="1776b-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="1776b-350">たとえば、「 `"/address/zipCode"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="1776b-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="1776b-351">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="1776b-352">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="1776b-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="1776b-353">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="1776b-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="1776b-354">オペレーション</span><span class="sxs-lookup"><span data-stu-id="1776b-354">Operations</span></span>

<span data-ttu-id="1776b-355">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="1776b-356">Operation</span><span class="sxs-lookup"><span data-stu-id="1776b-356">Operation</span></span>  | <span data-ttu-id="1776b-357">Notes</span><span class="sxs-lookup"><span data-stu-id="1776b-357">Notes</span></span> |
|---
<span data-ttu-id="1776b-358">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-359">'Blazor'</span></span>
- <span data-ttu-id="1776b-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-360">'Identity'</span></span>
- <span data-ttu-id="1776b-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-362">'Razor'</span></span>
- <span data-ttu-id="1776b-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-364">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-365">'Blazor'</span></span>
- <span data-ttu-id="1776b-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-366">'Identity'</span></span>
- <span data-ttu-id="1776b-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-368">'Razor'</span></span>
- <span data-ttu-id="1776b-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-370">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-371">'Blazor'</span></span>
- <span data-ttu-id="1776b-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-372">'Identity'</span></span>
- <span data-ttu-id="1776b-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-374">'Razor'</span></span>
- <span data-ttu-id="1776b-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-375">'SignalR' uid:</span></span> 

<span data-ttu-id="1776b-376">------|---
タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-377">'Blazor'</span></span>
- <span data-ttu-id="1776b-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-378">'Identity'</span></span>
- <span data-ttu-id="1776b-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-380">'Razor'</span></span>
- <span data-ttu-id="1776b-381">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-382">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-383">'Blazor'</span></span>
- <span data-ttu-id="1776b-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-384">'Identity'</span></span>
- <span data-ttu-id="1776b-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-386">'Razor'</span></span>
- <span data-ttu-id="1776b-387">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-388">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-389">'Blazor'</span></span>
- <span data-ttu-id="1776b-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-390">'Identity'</span></span>
- <span data-ttu-id="1776b-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-392">'Razor'</span></span>
- <span data-ttu-id="1776b-393">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-394">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-395">'Blazor'</span></span>
- <span data-ttu-id="1776b-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-396">'Identity'</span></span>
- <span data-ttu-id="1776b-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-398">'Razor'</span></span>
- <span data-ttu-id="1776b-399">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-400">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-401">'Blazor'</span></span>
- <span data-ttu-id="1776b-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-402">'Identity'</span></span>
- <span data-ttu-id="1776b-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-404">'Razor'</span></span>
- <span data-ttu-id="1776b-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-406">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-407">'Blazor'</span></span>
- <span data-ttu-id="1776b-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-408">'Identity'</span></span>
- <span data-ttu-id="1776b-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-410">'Razor'</span></span>
- <span data-ttu-id="1776b-411">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-412">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-413">'Blazor'</span></span>
- <span data-ttu-id="1776b-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-414">'Identity'</span></span>
- <span data-ttu-id="1776b-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-416">'Razor'</span></span>
- <span data-ttu-id="1776b-417">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-418">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-419">'Blazor'</span></span>
- <span data-ttu-id="1776b-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-420">'Identity'</span></span>
- <span data-ttu-id="1776b-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-422">'Razor'</span></span>
- <span data-ttu-id="1776b-423">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-424">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-425">'Blazor'</span></span>
- <span data-ttu-id="1776b-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-426">'Identity'</span></span>
- <span data-ttu-id="1776b-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-428">'Razor'</span></span>
- <span data-ttu-id="1776b-429">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-430">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-431">'Blazor'</span></span>
- <span data-ttu-id="1776b-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-432">'Identity'</span></span>
- <span data-ttu-id="1776b-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-434">'Razor'</span></span>
- <span data-ttu-id="1776b-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-436">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-437">'Blazor'</span></span>
- <span data-ttu-id="1776b-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-438">'Identity'</span></span>
- <span data-ttu-id="1776b-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-440">'Razor'</span></span>
- <span data-ttu-id="1776b-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-442">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-443">'Blazor'</span></span>
- <span data-ttu-id="1776b-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-444">'Identity'</span></span>
- <span data-ttu-id="1776b-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-446">'Razor'</span></span>
- <span data-ttu-id="1776b-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-448">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-449">'Blazor'</span></span>
- <span data-ttu-id="1776b-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-450">'Identity'</span></span>
- <span data-ttu-id="1776b-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-452">'Razor'</span></span>
- <span data-ttu-id="1776b-453">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1776b-454">タイトル: 作成者: 説明: ミリ秒。作成者: ms. カスタム: ms。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="1776b-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1776b-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1776b-455">'Blazor'</span></span>
- <span data-ttu-id="1776b-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1776b-456">'Identity'</span></span>
- <span data-ttu-id="1776b-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1776b-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="1776b-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1776b-458">'Razor'</span></span>
- <span data-ttu-id="1776b-459">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="1776b-459">'SignalR' uid:</span></span> 

<span data-ttu-id="1776b-460">----------------| |`add`     |プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="1776b-461">既存のプロパティの場合: 値を設定します |。|`remove`  |プロパティまたは配列の要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="1776b-462">| |`replace` |の `remove` 後に同じ場所を指定した場合と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="1776b-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="1776b-463">| |`move`    |Source から `remove` の値を使用して、ソースから宛先への変換元と同じ `add` です。</span><span class="sxs-lookup"><span data-stu-id="1776b-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="1776b-464">| |`copy`    |`add`Source の値を使用して destination と同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="1776b-465">| |`test`    |値が `path` = 指定されている場合 `value` 、成功の状態コードを返します |。</span><span class="sxs-lookup"><span data-stu-id="1776b-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="1776b-466">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="1776b-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="1776b-467">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="1776b-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="1776b-468">パッケージは、 [AspnetCore](xref:fundamentals/metapackage-app)メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="1776b-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="1776b-469">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="1776b-469">Action method code</span></span>

<span data-ttu-id="1776b-470">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="1776b-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="1776b-471">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="1776b-472">通常は `[FromBody]` を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="1776b-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="1776b-473">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="1776b-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="1776b-474">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1776b-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="1776b-475">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="1776b-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="1776b-476">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="1776b-476">The sample action method:</span></span>

* <span data-ttu-id="1776b-477">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="1776b-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="1776b-478">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="1776b-478">Applies the patch.</span></span>
* <span data-ttu-id="1776b-479">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="1776b-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="1776b-480">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="1776b-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="1776b-481">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="1776b-481">Model state</span></span>

<span data-ttu-id="1776b-482">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="1776b-483">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="1776b-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="1776b-484">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="1776b-485">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="1776b-485">Dynamic objects</span></span>

<span data-ttu-id="1776b-486">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="1776b-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="1776b-487">追加の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-487">The add operation</span></span>

* <span data-ttu-id="1776b-488">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1776b-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="1776b-489">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="1776b-490">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="1776b-491">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="1776b-492">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="1776b-493">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="1776b-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="1776b-494">削除の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-494">The remove operation</span></span>

* <span data-ttu-id="1776b-495">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="1776b-496">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="1776b-497">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="1776b-498">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="1776b-499">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="1776b-500">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="1776b-501">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="1776b-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="1776b-502">置換の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-502">The replace operation</span></span>

<span data-ttu-id="1776b-503">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="1776b-504">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1776b-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="1776b-505">移動の操作</span><span class="sxs-lookup"><span data-stu-id="1776b-505">The move operation</span></span>

* <span data-ttu-id="1776b-506">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="1776b-507">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="1776b-508">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="1776b-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="1776b-509">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="1776b-510">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1776b-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="1776b-511">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="1776b-511">The following sample patch document:</span></span>

* <span data-ttu-id="1776b-512">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="1776b-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="1776b-513">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="1776b-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="1776b-514">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="1776b-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="1776b-515">コピー操作</span><span class="sxs-lookup"><span data-stu-id="1776b-515">The copy operation</span></span>

<span data-ttu-id="1776b-516">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="1776b-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="1776b-517">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="1776b-517">The following sample patch document:</span></span>

* <span data-ttu-id="1776b-518">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="1776b-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="1776b-519">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="1776b-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="1776b-520">テストの操作</span><span class="sxs-lookup"><span data-stu-id="1776b-520">The test operation</span></span>

<span data-ttu-id="1776b-521">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="1776b-522">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="1776b-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="1776b-523">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1776b-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="1776b-524">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="1776b-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="1776b-525">コードを入手する</span><span class="sxs-lookup"><span data-stu-id="1776b-525">Get the code</span></span>

<span data-ttu-id="1776b-526">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="1776b-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="1776b-527">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1776b-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1776b-528">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="1776b-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="1776b-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="1776b-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="1776b-530">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="1776b-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="1776b-531">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="1776b-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="1776b-532">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="1776b-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1776b-533">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1776b-533">Additional resources</span></span>

* [<span data-ttu-id="1776b-534">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="1776b-535">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="1776b-536">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="1776b-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="1776b-537">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="1776b-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="1776b-538">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="1776b-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="1776b-539">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="1776b-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
