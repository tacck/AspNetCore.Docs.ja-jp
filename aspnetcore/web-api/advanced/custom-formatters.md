---
title: ASP.NET Core Web API のカスタム フォーマッタ
author: rick-anderson
description: ASP.NET Core で Web API のカスタム フォーマッタを作成して使用する方法を説明します。
ms.author: riande
ms.date: 06/25/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058923"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="97fa5-103">ASP.NET Core Web API のカスタム フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="97fa5-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="97fa5-104">[Kirk Larkin](https://twitter.com/serpent5)と[Tom Dykstra](https://github.com/tdykstra)。</span><span class="sxs-lookup"><span data-stu-id="97fa5-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="97fa5-105">ASP.NET Core MVC は、入力と出力のフォーマッタを使用した Web API でのデータ交換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="97fa5-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="97fa5-106">入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="97fa5-107">出力フォーマッタは、 [応答を書式設定](xref:web-api/advanced/formatting)するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="97fa5-108">フレームワークには、JSON および XML 用の組み込みの入力および出力フォーマッタが用意されています。</span><span class="sxs-lookup"><span data-stu-id="97fa5-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="97fa5-109">プレーン テキスト用の組み込みの出力フォーマッタが用意されていますが、プレーン テキスト用の入力フォーマッタは用意されていません。</span><span class="sxs-lookup"><span data-stu-id="97fa5-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="97fa5-110">この記事では、カスタム フォーマッタを作成して、追加形式のサポートを追加する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="97fa5-111">カスタムプレーンテキスト入力フォーマッタの例については、GitHub の [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97fa5-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="97fa5-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="97fa5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="97fa5-113">カスタム フォーマッタを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="97fa5-113">When to use custom formatters</span></span>

<span data-ttu-id="97fa5-114">カスタムフォーマッタを使用して、組み込みのフォーマッタによって処理されないコンテンツの種類のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="97fa5-115">カスタム フォーマッタの使用方法の概要</span><span class="sxs-lookup"><span data-stu-id="97fa5-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="97fa5-116">カスタムフォーマッタを作成するには:</span><span class="sxs-lookup"><span data-stu-id="97fa5-116">To create a custom formatter:</span></span>

* <span data-ttu-id="97fa5-117">クライアントに送信されるデータをシリアル化するには、出力フォーマッタクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="97fa5-118">クライアントから受信したデータを逆シリアル化するには、入力フォーマッタクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="97fa5-119">のコレクションおよびのコレクションに、フォーマッタクラスのインスタンスを追加 `InputFormatters` `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="97fa5-120">カスタム フォーマッタ クラスの作成方法</span><span class="sxs-lookup"><span data-stu-id="97fa5-120">How to create a custom formatter class</span></span>

<span data-ttu-id="97fa5-121">フォーマッタを作成する場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-121">To create a formatter:</span></span>

* <span data-ttu-id="97fa5-122">クラスを適切な基底クラスから派生させます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="97fa5-123">このサンプルアプリは、およびから派生して <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> います。</span><span class="sxs-lookup"><span data-stu-id="97fa5-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="97fa5-124">コンストラクターで有効なメディアの種類とエンコーディングを指定します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="97fa5-125"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> および <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="97fa5-126"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> および `WriteResponseBodyAsync` メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="97fa5-127">次のコードは、サンプルのクラスを示してい `VcardOutputFormatter` ます。 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)</span><span class="sxs-lookup"><span data-stu-id="97fa5-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="97fa5-128">適切な基底クラスからの派生</span><span class="sxs-lookup"><span data-stu-id="97fa5-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="97fa5-129">テキストメディアの種類 (たとえば、vCard) の場合 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> は、またはの基底クラスから派生し <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="97fa5-130">バイナリ型の場合 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> は、またはの基底クラスから派生し <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="97fa5-131">有効なメディアの種類とエンコーディングの指定</span><span class="sxs-lookup"><span data-stu-id="97fa5-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="97fa5-132">コンストラクターで、`SupportedMediaTypes` および `SupportedEncodings` コレクションに追加して、有効なメディアの種類とエンコーディングを指定します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="97fa5-133">フォーマッタクラスは、依存関係にコンストラクターの挿入を使用でき **ません** 。</span><span class="sxs-lookup"><span data-stu-id="97fa5-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="97fa5-134">たとえば、を `ILogger<VcardOutputFormatter>` パラメーターとしてコンストラクターに追加することはできません。</span><span class="sxs-lookup"><span data-stu-id="97fa5-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="97fa5-135">サービスにアクセスするには、メソッドに渡されるコンテキストオブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="97fa5-136">この記事のコード例と [サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) では、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="97fa5-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="97fa5-137">CanReadType と Canreadtype のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="97fa5-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="97fa5-138">メソッドまたはメソッドをオーバーライドして、逆シリアル化またはシリアル化を行う型を指定し `CanReadType` `CanWriteType` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="97fa5-139">たとえば、型から vCard テキストを作成 `Contact` し、その逆も同様にします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="97fa5-140">CanWriteResult メソッド</span><span class="sxs-lookup"><span data-stu-id="97fa5-140">The CanWriteResult method</span></span>

<span data-ttu-id="97fa5-141">一部のシナリオでは、をで `CanWriteResult` はなくオーバーライドする必要があり `CanWriteType` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="97fa5-142">次のすべての条件が満たされている場合は、`CanWriteResult` を使用します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="97fa5-143">アクションメソッドは、モデルクラスを返します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-143">The action method returns a model class.</span></span>
* <span data-ttu-id="97fa5-144">実行時に返される可能性がある派生クラスがあります。</span><span class="sxs-lookup"><span data-stu-id="97fa5-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="97fa5-145">アクションによって返される派生クラスは、実行時に既知である必要があります。</span><span class="sxs-lookup"><span data-stu-id="97fa5-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="97fa5-146">たとえば、次のようなアクションメソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="97fa5-147">シグネチャは型を返し `Person` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="97fa5-148">`Student`は、 `Instructor` から派生した型または型を返すことができ `Person` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="97fa5-149">フォーマッタがオブジェクトのみを処理するようにするには、 `Student` <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> メソッドに提供されたコンテキストオブジェクトのの型を確認し `CanWriteResult` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="97fa5-150">アクションメソッドがを返す場合 `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="97fa5-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="97fa5-151">を使用する必要はありません `CanWriteResult` 。</span><span class="sxs-lookup"><span data-stu-id="97fa5-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="97fa5-152">メソッドは、 `CanWriteType` ランタイム型を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="97fa5-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="97fa5-153">ReadRequestBodyAsync と WriteResponseBodyAsync のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="97fa5-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="97fa5-154">逆シリアル化またはシリアル化は、またはで実行され `ReadRequestBodyAsync` `WriteResponseBodyAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="97fa5-155">次の例は、依存関係挿入コンテナーからサービスを取得する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="97fa5-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="97fa5-156">コンストラクターのパラメーターからサービスを取得できません。</span><span class="sxs-lookup"><span data-stu-id="97fa5-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="97fa5-157">カスタム フォーマッタを使用するように MVC を構成する方法</span><span class="sxs-lookup"><span data-stu-id="97fa5-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="97fa5-158">カスタム フォーマッタを使用するには、`InputFormatters` または `OutputFormatters` コレクションにフォーマッタ クラスのインスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="97fa5-159">フォーマッタは、挿入した順序で評価されます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="97fa5-160">最初のものが優先されます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="97fa5-161">完全な `VcardInputFormatter` クラス</span><span class="sxs-lookup"><span data-stu-id="97fa5-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="97fa5-162">次のコードは、サンプルのクラスを示してい `VcardInputFormatter` ます。 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)</span><span class="sxs-lookup"><span data-stu-id="97fa5-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="97fa5-163">アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="97fa5-163">Test the app</span></span>

<span data-ttu-id="97fa5-164">[この記事のサンプルアプリを実行](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)します。これにより、基本的な vCard 入力フォーマッタと出力フォーマッタが実装されます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="97fa5-165">アプリは次のような Vcard を読み取り、書き込みます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="97fa5-166">VCard の出力を表示するには、アプリを実行し、Accept ヘッダーを含む Get 要求をに送信し `text/vcard` `https://localhost:5001/api/contacts` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="97fa5-167">アドレス帳のメモリ内コレクションに vCard を追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="97fa5-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="97fa5-168">`Post`Postman などのツールを使用してに要求を送信し `/api/contacts` ます。</span><span class="sxs-lookup"><span data-stu-id="97fa5-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="97fa5-169">`Content-Type` ヘッダーを `text/vcard` に設定します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="97fa5-170">`vCard`本文には、前の例のように書式設定されたテキストを設定します。</span><span class="sxs-lookup"><span data-stu-id="97fa5-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97fa5-171">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="97fa5-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
