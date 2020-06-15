---
title: ASP.NET Core での要求と応答の操作
author: jkotalik
description: ASP.NET Core で要求本文の読み取りと応答本文の書き込みを行う方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: fed9e48cdb2b33805cb05243de706b5c86853328
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84548533"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="6d56c-103">ASP.NET Core での要求と応答の操作</span><span class="sxs-lookup"><span data-stu-id="6d56c-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="6d56c-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="6d56c-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="6d56c-105">この記事では、要求本文からの読み取りと、応答本文への書き込みを行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="6d56c-106">ミドルウェアを作成するときは、これらの操作のコードが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="6d56c-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="6d56c-107">操作は MVC と Razor Pages によって処理されるため、ミドルウェアの作成以外では、通常、カスタムコードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="6d56c-108">要求と応答の本文には 2 つの抽象化があります: <xref:System.IO.Stream> と <xref:System.IO.Pipelines.Pipe> です。</span><span class="sxs-lookup"><span data-stu-id="6d56c-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="6d56c-109">要求の読み取りでは、<xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> が <xref:System.IO.Stream> で、`HttpRequest.BodyReader` が <xref:System.IO.Pipelines.PipeReader> です。</span><span class="sxs-lookup"><span data-stu-id="6d56c-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="6d56c-110">応答の書き込みでは、<xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> が <xref:System.IO.Stream> で、`HttpResponse.BodyWriter` が <xref:System.IO.Pipelines.PipeWriter> です。</span><span class="sxs-lookup"><span data-stu-id="6d56c-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="6d56c-111">[パイプライン](/dotnet/standard/io/pipelines)は、ストリームよりも推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="6d56c-112">一部の単純な操作ではストリームの方が使いやすい場合がありますが、パイプラインの方がパフォーマンスに優れていて、ほとんどのシナリオでより簡単に使えます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="6d56c-113">ASP.NET Core では、内部的にストリームではなくパイプラインが使われ始めています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="6d56c-114">その例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6d56c-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="6d56c-115">ストリームがフレームワークから削除されていません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="6d56c-116">ストリームは .NET 全体で使われ続けます。また、ストリームの種類の多くにはパイプラインに相当するものがありません (`FileStreams` や `ResponseCompression` など)。</span><span class="sxs-lookup"><span data-stu-id="6d56c-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="6d56c-117">ストリームの例</span><span class="sxs-lookup"><span data-stu-id="6d56c-117">Stream examples</span></span>

<span data-ttu-id="6d56c-118">要求本文全体を、改行で分割した文字列のリストとして読み取るミドルウェアを作成したいとします。</span><span class="sxs-lookup"><span data-stu-id="6d56c-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="6d56c-119">単純なストリームの実装は、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="6d56c-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="6d56c-120">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-120">The following code:</span></span>
> * <span data-ttu-id="6d56c-121">要求本文の読み取りにパイプが使用されない問題を示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="6d56c-122">実稼働アプリで使用する意図はありません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="6d56c-123">このコードで動作しますが、いくつか問題があります。</span><span class="sxs-lookup"><span data-stu-id="6d56c-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="6d56c-124">例では、`StringBuilder` に追加する前に、すぐに破棄される別の文字列 (`encodedString`) を作成しています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="6d56c-125">このプロセスはストリーム内のすべてのバイトに対して実行されるため、要求本文全体のサイズよりも余分にメモリの割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="6d56c-126">例では、改行で分割する前に文字列全体を読み取っています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="6d56c-127">バイト配列内で改行をチェックした方がより効率的です。</span><span class="sxs-lookup"><span data-stu-id="6d56c-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="6d56c-128">これらの問題をいくつか修正した例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="6d56c-129">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-129">The following code:</span></span>
> * <span data-ttu-id="6d56c-130">全部の問題が解消されるわけではありませんが、前のコードの一部の問題を解決する策を示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="6d56c-131">実稼働アプリで使用する意図はありません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="6d56c-132">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="6d56c-132">This preceding example:</span></span>

* <span data-ttu-id="6d56c-133">要求本文全体を `StringBuilder` にバッファーすることがありません (改行文字がない場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="6d56c-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="6d56c-134">文字列の `Split` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="6d56c-135">ただし、まだいくつかの問題が残っています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="6d56c-136">改行文字がスパースだった場合、要求本文の多くが文字列にバッファーされます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="6d56c-137">コードで依然として文字列 (`remainingString`) が作成され、文字列バッファーに追加されているため、余分な割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="6d56c-138">これらの問題は修正可能ですが、わずかな改善でコードがますます複雑になっていきます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="6d56c-139">パイプラインには、これらの問題を、コードの複雑さを最小限に抑えて解決する方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="6d56c-140">パイプライン</span><span class="sxs-lookup"><span data-stu-id="6d56c-140">Pipelines</span></span>

<span data-ttu-id="6d56c-141">同じシナリオを、[PipeReader](/dotnet/standard/io/pipelines#pipe) を使って処理する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="6d56c-142">この例では、ストリームの実装で発生していた多くの問題が修正されています。</span><span class="sxs-lookup"><span data-stu-id="6d56c-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="6d56c-143">使われていないバイトを `PipeReader` で処理するため、文字列バッファーが不要です。</span><span class="sxs-lookup"><span data-stu-id="6d56c-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="6d56c-144">エンコードされた文字列は、返される文字列のリストに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="6d56c-145">`ToArray` 呼び出しと、文字列で使用されるメモリを除き、文字列作成では割り当てが発生しません。</span><span class="sxs-lookup"><span data-stu-id="6d56c-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="6d56c-146">アダプター</span><span class="sxs-lookup"><span data-stu-id="6d56c-146">Adapters</span></span>

<span data-ttu-id="6d56c-147">`HttpRequest` と `HttpResponse` には、`Body`、`BodyReader`、`BodyWriter` プロパティを利用できます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="6d56c-148">`Body` を別のストリームに設定すると、新しいアダプターのセットにより、各種類が別のものに自動的に適応します。</span><span class="sxs-lookup"><span data-stu-id="6d56c-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="6d56c-149">`HttpRequest.Body` を新しいストリームに設定した場合、`HttpRequest.BodyReader` は自動的に、`HttpRequest.Body` をラップする新しい `PipeReader` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="6d56c-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="6d56c-150">StartAsync</span></span>

<span data-ttu-id="6d56c-151">`HttpResponse.StartAsync` は、ヘッダーが変更不可能であり、また `OnStarting` コールバックを実行することを示すために使います。</span><span class="sxs-lookup"><span data-stu-id="6d56c-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="6d56c-152">サーバーとして Kestrel を使う場合、`PipeReader` を使う前に `StartAsync` を呼び出すことで、`GetMemory` によって返されるメモリが、外部バッファーではなく Kestrel の内部 <xref:System.IO.Pipelines.Pipe> に属するよう保証できます。</span><span class="sxs-lookup"><span data-stu-id="6d56c-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d56c-153">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6d56c-153">Additional resources</span></span>

* [<span data-ttu-id="6d56c-154">.NET の System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="6d56c-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
