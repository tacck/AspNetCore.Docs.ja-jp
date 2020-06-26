---
title: ASP.NET Core でのカスタム モデル バインド
author: ardalis
description: モデル バインドにより ASP.NET Core のモデルの型を使用して、コントローラー アクションが直接動作する方法について説明します。
ms.author: riande
ms.date: 01/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 54080191d76df674444019d43180a7f9d84b471c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403639"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="4b054-103">ASP.NET Core でのカスタム モデル バインド</span><span class="sxs-lookup"><span data-stu-id="4b054-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b054-104">作成者: [Steve Smith](https://ardalis.com/)、[Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4b054-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="4b054-105">モデル バインドにより、コントローラー アクションが HTTP 要求ではなく (メソッド引数として渡される) モデルの型を直接操作できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4b054-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="4b054-106">受信要求データとアプリケーション モデルのマッピングは、モデル バインダーによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="4b054-107">開発者は、カスタム モデル バインダーを実装することで組み込みのモデル バインド機能を拡張することができます (ただし、通常は自分のプロバイダーを記述する必要はありません)。</span><span class="sxs-lookup"><span data-stu-id="4b054-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="4b054-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4b054-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="4b054-109">既定のモデル バインダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="4b054-109">Default model binder limitations</span></span>

<span data-ttu-id="4b054-110">既定のモデル バインダーは、一般的な .NET Core データ型の多くをサポートし、ほとんどの開発者のニーズを満たします。</span><span class="sxs-lookup"><span data-stu-id="4b054-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="4b054-111">要求からのテキストベースの入力をモデルの型に直接バインドすることが見込まれています。</span><span class="sxs-lookup"><span data-stu-id="4b054-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="4b054-112">入力は、バインドする前に変換しなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="4b054-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="4b054-113">たとえば、モデル データの検索に使用できるキーがある場合などです。</span><span class="sxs-lookup"><span data-stu-id="4b054-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="4b054-114">カスタム モデル バインダーを使用すると、キーに基づいてデータをフェッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="4b054-115">モデル バインドの確認</span><span class="sxs-lookup"><span data-stu-id="4b054-115">Model binding review</span></span>

<span data-ttu-id="4b054-116">モデル バインドは、操作の対象とする型に特定の定義を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b054-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="4b054-117">*単純型*は、入力の 1 つの文字列から変換されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="4b054-118">*複合型*は、複数の入力値から変換されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="4b054-119">フレームワークは、`TypeConverter` の存在の有無によって違いを判断します。</span><span class="sxs-lookup"><span data-stu-id="4b054-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="4b054-120">外部リソースを必要としない `string` -> `SomeType` の単純なマッピングがある場合は型コンバーターを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4b054-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="4b054-121">独自のカスタム モデル バインダーを作成する前に、既存のモデル バインダーがどのように実装されているかを確認するとよいでしょう。</span><span class="sxs-lookup"><span data-stu-id="4b054-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="4b054-122">Base64 でエンコードされた文字列をバイト配列に変換できる、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4b054-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="4b054-123">バイト配列は多くの場合、ファイルまたはデータベース BLOB のフィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="4b054-124">ByteArrayModelBinder の操作</span><span class="sxs-lookup"><span data-stu-id="4b054-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="4b054-125">Base64 でエンコードされた文字列を使用してバイナリ データを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="4b054-126">たとえば、イメージは文字列としてエンコードできます。</span><span class="sxs-lookup"><span data-stu-id="4b054-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="4b054-127">このサンプルには、[Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt) 内に Base64 でエンコードされた文字列としてのイメージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4b054-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="4b054-128">ASP.NET Core MVC では Base64 でエンコードされた文字列を取得し、`ByteArrayModelBinder` を使用してこれをバイト配列に変換できます。</span><span class="sxs-lookup"><span data-stu-id="4b054-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="4b054-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> により `byte[]` 引数が `ByteArrayModelBinder` にマップされます。</span><span class="sxs-lookup"><span data-stu-id="4b054-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="4b054-130">独自のカスタム モデル バインダーを作成するときには、独自の `IModelBinderProvider` 型を実装するか、<xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="4b054-131">次の例は、`ByteArrayModelBinder` を使用して Base64 でエンコードされた文字列を `byte[]` に変換し、結果をファイルに保存する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="4b054-132">[Postman](https://www.getpostman.com/) のようなツールを使用すると、この API メソッドに Base64 でエンコードされた文字列を POST することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="4b054-133">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="4b054-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="4b054-134">バインダーが要求データを適切に命名されたプロパティまたは引数にバインドできれば、モデル バインドは成功します。</span><span class="sxs-lookup"><span data-stu-id="4b054-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="4b054-135">ビュー モデルを指定して `ByteArrayModelBinder` を使用する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="4b054-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="4b054-136">カスタム モデル バインダーのサンプル</span><span class="sxs-lookup"><span data-stu-id="4b054-136">Custom model binder sample</span></span>

<span data-ttu-id="4b054-137">このセクションでは、次のようなカスタム モデル バインダーを実装します。</span><span class="sxs-lookup"><span data-stu-id="4b054-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="4b054-138">受信した要求データを厳密に型指定されたキーの引数に変換する。</span><span class="sxs-lookup"><span data-stu-id="4b054-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="4b054-139">Entity Framework Core を使用して関連するエンティティをフェッチする。</span><span class="sxs-lookup"><span data-stu-id="4b054-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="4b054-140">関連するエンティティを引数としてアクション メソッドに渡す。</span><span class="sxs-lookup"><span data-stu-id="4b054-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="4b054-141">次のサンプルでは、`Author` モデルの `ModelBinder` 属性を使用しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="4b054-142">上記のコードでは、`Author` アクション パラメーターのバインドで使用される必要がある `IModelBinder` の型が `ModelBinder` 属性で指定されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="4b054-143">次の `AuthorEntityBinder` クラスは、Entity Framework Core と `authorId` を使用してデータ ソースからエンティティをフェッチすることで `Author` パラメーターをバインドします。</span><span class="sxs-lookup"><span data-stu-id="4b054-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="4b054-144">前述の `AuthorEntityBinder` クラスは、カスタム モデル バインダーを示しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="4b054-145">このクラスは、参照シナリオのベスト プラクティスを示すものではありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="4b054-146">参照の場合は、`authorId` をバインドし、アクション メソッドでデータベースをクエリします。</span><span class="sxs-lookup"><span data-stu-id="4b054-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="4b054-147">この方法により、`NotFound` のケースからモデル バインドのエラーが分離されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="4b054-148">アクション メソッドでの `AuthorEntityBinder` の使用方法を次のコードに示します。</span><span class="sxs-lookup"><span data-stu-id="4b054-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="4b054-149">`ModelBinder` 属性を使用すると、既定の規則を使用しないパラメーターに `AuthorEntityBinder` を適用できます。</span><span class="sxs-lookup"><span data-stu-id="4b054-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="4b054-150">この例では、引数の名前が既定の `authorId` ではないため、`ModelBinder` 属性を使用してパラメーターに指定されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="4b054-151">アクション メソッドでエンティティを検索する場合と比較して、コントローラーとアクション メソッドの両方が簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="4b054-152">Entity Framework Core を使用して作成者をフェッチするためのロジックは、モデル バインダーに移動しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="4b054-153">これは、`Author` モデルにバインドするメソッドがいくつかある場合、大幅な簡略化になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="4b054-154">`ModelBinder` 属性を (ビューモデルなどの) 個々のモデル プロパティまたはアクション メソッド パラメーターに適用して、その型やアクションのみを対象とする特定のモデル バインダーまたはモデル名を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="4b054-155">ModelBinderProvider の実装</span><span class="sxs-lookup"><span data-stu-id="4b054-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="4b054-156">属性を適用する代わりに、`IModelBinderProvider` を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="4b054-157">これは、組み込みフレームワーク バインダーの実装方法と同じです。</span><span class="sxs-lookup"><span data-stu-id="4b054-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="4b054-158">バインダーが動作する型を指定するときに、バインダーが受け入れる入力**ではなく**、生成される引数の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="4b054-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="4b054-159">次のバインダー プロバイダーは `AuthorEntityBinder` で動作します。</span><span class="sxs-lookup"><span data-stu-id="4b054-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="4b054-160">プロバイダーの MVC のコレクションに追加されるときに、`Author` または `Author` の型のパラメーターで `ModelBinder` 属性を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="4b054-161">注: 上のコードは `BinderTypeModelBinder` を返します。</span><span class="sxs-lookup"><span data-stu-id="4b054-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="4b054-162">`BinderTypeModelBinder` はモデル バインダーのファクトリとして機能し、依存関係の挿入 (DI) を提供します。</span><span class="sxs-lookup"><span data-stu-id="4b054-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="4b054-163">`AuthorEntityBinder` は DI に EF Core へのアクセスを求めます。</span><span class="sxs-lookup"><span data-stu-id="4b054-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="4b054-164">モデル バインダーが DI からのサービスを必要としている場合は、`BinderTypeModelBinder` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="4b054-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="4b054-165">カスタム モデル バインダー プロバイダーを使用する場合、次のようにしてこれを `ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4b054-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="4b054-166">モデル バインダーを評価するときに、プロバイダーのコレクションが順序どおりにチェックされます。</span><span class="sxs-lookup"><span data-stu-id="4b054-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="4b054-167">バインダーを返す最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="4b054-168">コレクションの末尾にプロバイダーを追加すると、カスタム バインダーより前に組み込みのモデル バインダーが呼び出される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="4b054-169">この例では、カスタム プロバイダーが `Author` アクションの引数で使用されるように、コレクションの先頭に追加されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="4b054-170">ポリモーフィック モデル バインド</span><span class="sxs-lookup"><span data-stu-id="4b054-170">Polymorphic model binding</span></span>

<span data-ttu-id="4b054-171">派生型の異なるモデルへのバインドは、ポリモーフィック モデル バインドと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="4b054-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="4b054-172">ポリモーフィック カスタム モデル バインドは、要求値を特定の派生モデル型にバインドする必要がある場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="4b054-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="4b054-173">ポリモーフィック モデル バインド:</span><span class="sxs-lookup"><span data-stu-id="4b054-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="4b054-174">すべての言語と相互運用できるように設計された REST API では一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="4b054-175">バインドされたモデルに関する判断が困難になります。</span><span class="sxs-lookup"><span data-stu-id="4b054-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="4b054-176">ただし、アプリにポリモーフィック モデル バインドが必要な場合、実装は次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="4b054-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="4b054-177">推奨事項とベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="4b054-177">Recommendations and best practices</span></span>

<span data-ttu-id="4b054-178">カスタム モデル バインダー:</span><span class="sxs-lookup"><span data-stu-id="4b054-178">Custom model binders:</span></span>

- <span data-ttu-id="4b054-179">状態コードの設定または結果 (たとえば 404 Not Found) のリターンを試行しないでください。</span><span class="sxs-lookup"><span data-stu-id="4b054-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="4b054-180">モデル バインドが失敗した場合、アクション メソッド自体の[アクション フィルター](xref:mvc/controllers/filters)またはロジックでエラーが処理される必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="4b054-181">アクション メソッドから繰り返しのコードや横断的な問題を排除する場合に最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4b054-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="4b054-182">通常、文字列をカスタムの型に変換する場合に使用すべきではありません。通常は、<xref:System.ComponentModel.TypeConverter> の方がオプションとして優れています。</span><span class="sxs-lookup"><span data-stu-id="4b054-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b054-183">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4b054-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4b054-184">モデル バインドにより、コントローラー アクションが HTTP 要求ではなく (メソッド引数として渡される) モデルの型を直接操作できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4b054-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="4b054-185">受信要求データとアプリケーション モデルのマッピングは、モデル バインダーによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="4b054-186">開発者は、カスタム モデル バインダーを実装することで組み込みのモデル バインド機能を拡張することができます (ただし、通常は自分のプロバイダーを記述する必要はありません)。</span><span class="sxs-lookup"><span data-stu-id="4b054-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="4b054-187">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4b054-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="4b054-188">既定のモデル バインダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="4b054-188">Default model binder limitations</span></span>

<span data-ttu-id="4b054-189">既定のモデル バインダーは、一般的な .NET Core データ型の多くをサポートし、ほとんどの開発者のニーズを満たします。</span><span class="sxs-lookup"><span data-stu-id="4b054-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="4b054-190">要求からのテキストベースの入力をモデルの型に直接バインドすることが見込まれています。</span><span class="sxs-lookup"><span data-stu-id="4b054-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="4b054-191">入力は、バインドする前に変換しなければならないことがあります。</span><span class="sxs-lookup"><span data-stu-id="4b054-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="4b054-192">たとえば、モデル データの検索に使用できるキーがある場合などです。</span><span class="sxs-lookup"><span data-stu-id="4b054-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="4b054-193">カスタム モデル バインダーを使用すると、キーに基づいてデータをフェッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="4b054-194">モデル バインドの確認</span><span class="sxs-lookup"><span data-stu-id="4b054-194">Model binding review</span></span>

<span data-ttu-id="4b054-195">モデル バインドは、操作の対象とする型に特定の定義を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b054-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="4b054-196">*単純型*は、入力の 1 つの文字列から変換されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="4b054-197">*複合型*は、複数の入力値から変換されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="4b054-198">フレームワークは、`TypeConverter` の存在の有無によって違いを判断します。</span><span class="sxs-lookup"><span data-stu-id="4b054-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="4b054-199">外部リソースを必要としない `string` -> `SomeType` の単純なマッピングがある場合は型コンバーターを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4b054-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="4b054-200">独自のカスタム モデル バインダーを作成する前に、既存のモデル バインダーがどのように実装されているかを確認するとよいでしょう。</span><span class="sxs-lookup"><span data-stu-id="4b054-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="4b054-201">Base64 でエンコードされた文字列をバイト配列に変換できる、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4b054-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="4b054-202">バイト配列は多くの場合、ファイルまたはデータベース BLOB のフィールドとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="4b054-203">ByteArrayModelBinder の操作</span><span class="sxs-lookup"><span data-stu-id="4b054-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="4b054-204">Base64 でエンコードされた文字列を使用してバイナリ データを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="4b054-205">たとえば、イメージは文字列としてエンコードできます。</span><span class="sxs-lookup"><span data-stu-id="4b054-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="4b054-206">このサンプルには、[Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt) 内に Base64 でエンコードされた文字列としてのイメージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4b054-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="4b054-207">ASP.NET Core MVC では Base64 でエンコードされた文字列を取得し、`ByteArrayModelBinder` を使用してこれをバイト配列に変換できます。</span><span class="sxs-lookup"><span data-stu-id="4b054-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="4b054-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> により `byte[]` 引数が `ByteArrayModelBinder` にマップされます。</span><span class="sxs-lookup"><span data-stu-id="4b054-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="4b054-209">独自のカスタム モデル バインダーを作成するときには、独自の `IModelBinderProvider` 型を実装するか、<xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="4b054-210">次の例は、`ByteArrayModelBinder` を使用して Base64 でエンコードされた文字列を `byte[]` に変換し、結果をファイルに保存する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="4b054-211">[Postman](https://www.getpostman.com/) のようなツールを使用すると、この API メソッドに Base64 でエンコードされた文字列を POST することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="4b054-212">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="4b054-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="4b054-213">バインダーが要求データを適切に命名されたプロパティまたは引数にバインドできれば、モデル バインドは成功します。</span><span class="sxs-lookup"><span data-stu-id="4b054-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="4b054-214">ビュー モデルを指定して `ByteArrayModelBinder` を使用する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="4b054-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="4b054-215">カスタム モデル バインダーのサンプル</span><span class="sxs-lookup"><span data-stu-id="4b054-215">Custom model binder sample</span></span>

<span data-ttu-id="4b054-216">このセクションでは、次のようなカスタム モデル バインダーを実装します。</span><span class="sxs-lookup"><span data-stu-id="4b054-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="4b054-217">受信した要求データを厳密に型指定されたキーの引数に変換する。</span><span class="sxs-lookup"><span data-stu-id="4b054-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="4b054-218">Entity Framework Core を使用して関連するエンティティをフェッチする。</span><span class="sxs-lookup"><span data-stu-id="4b054-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="4b054-219">関連するエンティティを引数としてアクション メソッドに渡す。</span><span class="sxs-lookup"><span data-stu-id="4b054-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="4b054-220">次のサンプルでは、`Author` モデルの `ModelBinder` 属性を使用しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="4b054-221">上記のコードでは、`Author` アクション パラメーターのバインドで使用される必要がある `IModelBinder` の型が `ModelBinder` 属性で指定されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="4b054-222">次の `AuthorEntityBinder` クラスは、Entity Framework Core と `authorId` を使用してデータ ソースからエンティティをフェッチすることで `Author` パラメーターをバインドします。</span><span class="sxs-lookup"><span data-stu-id="4b054-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="4b054-223">前述の `AuthorEntityBinder` クラスは、カスタム モデル バインダーを示しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="4b054-224">このクラスは、参照シナリオのベスト プラクティスを示すものではありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="4b054-225">参照の場合は、`authorId` をバインドし、アクション メソッドでデータベースをクエリします。</span><span class="sxs-lookup"><span data-stu-id="4b054-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="4b054-226">この方法により、`NotFound` のケースからモデル バインドのエラーが分離されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="4b054-227">アクション メソッドでの `AuthorEntityBinder` の使用方法を次のコードに示します。</span><span class="sxs-lookup"><span data-stu-id="4b054-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="4b054-228">`ModelBinder` 属性を使用すると、既定の規則を使用しないパラメーターに `AuthorEntityBinder` を適用できます。</span><span class="sxs-lookup"><span data-stu-id="4b054-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="4b054-229">この例では、引数の名前が既定の `authorId` ではないため、`ModelBinder` 属性を使用してパラメーターに指定されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="4b054-230">アクション メソッドでエンティティを検索する場合と比較して、コントローラーとアクション メソッドの両方が簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="4b054-231">Entity Framework Core を使用して作成者をフェッチするためのロジックは、モデル バインダーに移動しています。</span><span class="sxs-lookup"><span data-stu-id="4b054-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="4b054-232">これは、`Author` モデルにバインドするメソッドがいくつかある場合、大幅な簡略化になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="4b054-233">`ModelBinder` 属性を (ビューモデルなどの) 個々のモデル プロパティまたはアクション メソッド パラメーターに適用して、その型やアクションのみを対象とする特定のモデル バインダーまたはモデル名を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="4b054-234">ModelBinderProvider の実装</span><span class="sxs-lookup"><span data-stu-id="4b054-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="4b054-235">属性を適用する代わりに、`IModelBinderProvider` を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="4b054-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="4b054-236">これは、組み込みフレームワーク バインダーの実装方法と同じです。</span><span class="sxs-lookup"><span data-stu-id="4b054-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="4b054-237">バインダーが動作する型を指定するときに、バインダーが受け入れる入力**ではなく**、生成される引数の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="4b054-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="4b054-238">次のバインダー プロバイダーは `AuthorEntityBinder` で動作します。</span><span class="sxs-lookup"><span data-stu-id="4b054-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="4b054-239">プロバイダーの MVC のコレクションに追加されるときに、`Author` または `Author` の型のパラメーターで `ModelBinder` 属性を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="4b054-240">注: 上のコードは `BinderTypeModelBinder` を返します。</span><span class="sxs-lookup"><span data-stu-id="4b054-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="4b054-241">`BinderTypeModelBinder` はモデル バインダーのファクトリとして機能し、依存関係の挿入 (DI) を提供します。</span><span class="sxs-lookup"><span data-stu-id="4b054-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="4b054-242">`AuthorEntityBinder` は DI に EF Core へのアクセスを求めます。</span><span class="sxs-lookup"><span data-stu-id="4b054-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="4b054-243">モデル バインダーが DI からのサービスを必要としている場合は、`BinderTypeModelBinder` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="4b054-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="4b054-244">カスタム モデル バインダー プロバイダーを使用する場合、次のようにしてこれを `ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4b054-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="4b054-245">モデル バインダーを評価するときに、プロバイダーのコレクションが順序どおりにチェックされます。</span><span class="sxs-lookup"><span data-stu-id="4b054-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="4b054-246">バインダーを返す最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4b054-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="4b054-247">コレクションの末尾にプロバイダーを追加すると、カスタム バインダーより前に組み込みのモデル バインダーが呼び出される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="4b054-248">この例では、カスタム プロバイダーが `Author` アクションの引数で使用されるように、コレクションの先頭に追加されています。</span><span class="sxs-lookup"><span data-stu-id="4b054-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="4b054-249">ポリモーフィック モデル バインド</span><span class="sxs-lookup"><span data-stu-id="4b054-249">Polymorphic model binding</span></span>

<span data-ttu-id="4b054-250">派生型の異なるモデルへのバインドは、ポリモーフィック モデル バインドと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="4b054-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="4b054-251">ポリモーフィック カスタム モデル バインドは、要求値を特定の派生モデル型にバインドする必要がある場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="4b054-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="4b054-252">ポリモーフィック モデル バインド:</span><span class="sxs-lookup"><span data-stu-id="4b054-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="4b054-253">すべての言語と相互運用できるように設計された REST API では一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="4b054-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="4b054-254">バインドされたモデルに関する判断が困難になります。</span><span class="sxs-lookup"><span data-stu-id="4b054-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="4b054-255">ただし、アプリにポリモーフィック モデル バインドが必要な場合、実装は次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="4b054-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="4b054-256">推奨事項とベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="4b054-256">Recommendations and best practices</span></span>

<span data-ttu-id="4b054-257">カスタム モデル バインダー:</span><span class="sxs-lookup"><span data-stu-id="4b054-257">Custom model binders:</span></span>

- <span data-ttu-id="4b054-258">状態コードの設定または結果 (たとえば 404 Not Found) のリターンを試行しないでください。</span><span class="sxs-lookup"><span data-stu-id="4b054-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="4b054-259">モデル バインドが失敗した場合、アクション メソッド自体の[アクション フィルター](xref:mvc/controllers/filters)またはロジックでエラーが処理される必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b054-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="4b054-260">アクション メソッドから繰り返しのコードや横断的な問題を排除する場合に最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4b054-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="4b054-261">通常、文字列をカスタムの型に変換する場合に使用すべきではありません。通常は、<xref:System.ComponentModel.TypeConverter> の方がオプションとして優れています。</span><span class="sxs-lookup"><span data-stu-id="4b054-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
