---
title: ASP.NET Core での要求と応答の操作
author: jkotalik
description: ASP.NET Core で要求本文の読み取りと応答本文の書き込みを行う方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: ce7357ccbb52736bfb44cd8e041c68a0992bf319
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627781"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core での要求と応答の操作

作成者: [Justin Kotalik](https://github.com/jkotalik)

この記事では、要求本文からの読み取りと、応答本文への書き込みを行う方法について説明します。 ミドルウェアを作成するときは、これらの操作のコードが必要になることがあります。 操作は MVC と Razor Pages によって処理されるため、ミドルウェアの作成以外では、通常、カスタムコードは必要ありません。

要求と応答の本文には 2 つの抽象化があります: <xref:System.IO.Stream> と <xref:System.IO.Pipelines.Pipe> です。 要求の読み取りでは、<xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> が <xref:System.IO.Stream> で、`HttpRequest.BodyReader` が <xref:System.IO.Pipelines.PipeReader> です。 応答の書き込みでは、<xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> が <xref:System.IO.Stream> で、`HttpResponse.BodyWriter` が <xref:System.IO.Pipelines.PipeWriter> です。

[パイプライン](/dotnet/standard/io/pipelines)は、ストリームよりも推奨されます。 一部の単純な操作ではストリームの方が使いやすい場合がありますが、パイプラインの方がパフォーマンスに優れていて、ほとんどのシナリオでより簡単に使えます。 ASP.NET Core では、内部的にストリームではなくパイプラインが使われ始めています。 その例は次のとおりです。

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

ストリームがフレームワークから削除されていません。 ストリームは .NET 全体で使われ続けます。また、ストリームの種類の多くにはパイプラインに相当するものがありません (`FileStreams` や `ResponseCompression` など)。

## <a name="stream-examples"></a>ストリームの例

要求本文全体を、改行で分割した文字列のリストとして読み取るミドルウェアを作成したいとします。 単純なストリームの実装は、次の例のようになります。

> [!WARNING]
> コード例を次に示します。
> * 要求本文の読み取りにパイプが使用されない問題を示します。
> * 実稼働アプリで使用する意図はありません。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

このコードで動作しますが、いくつか問題があります。

* 例では、`StringBuilder` に追加する前に、すぐに破棄される別の文字列 (`encodedString`) を作成しています。 このプロセスはストリーム内のすべてのバイトに対して実行されるため、要求本文全体のサイズよりも余分にメモリの割り当てが発生します。
* 例では、改行で分割する前に文字列全体を読み取っています。 バイト配列内で改行をチェックした方がより効率的です。

これらの問題をいくつか修正した例を次に示します。

> [!WARNING]
> コード例を次に示します。
> * 全部の問題が解消されるわけではありませんが、前のコードの一部の問題を解決する策を示します。
> * 実稼働アプリで使用する意図はありません。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

前の例の場合:

* 要求本文全体を `StringBuilder` にバッファーすることがありません (改行文字がない場合を除く)。
* 文字列の `Split` を呼び出しません。

ただし、まだいくつかの問題が残っています。

* 改行文字がスパースだった場合、要求本文の多くが文字列にバッファーされます。
* コードで依然として文字列 (`remainingString`) が作成され、文字列バッファーに追加されているため、余分な割り当てが発生します。

これらの問題は修正可能ですが、わずかな改善でコードがますます複雑になっていきます。 パイプラインには、これらの問題を、コードの複雑さを最小限に抑えて解決する方法が用意されています。

## <a name="pipelines"></a>パイプライン

同じシナリオを、[PipeReader](/dotnet/standard/io/pipelines#pipe) を使って処理する方法を次の例に示します。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

この例では、ストリームの実装で発生していた多くの問題が修正されています。

* 使われていないバイトを `PipeReader` で処理するため、文字列バッファーが不要です。
* エンコードされた文字列は、返される文字列のリストに直接追加されます。
* `ToArray` 呼び出しと、文字列で使用されるメモリを除き、文字列作成では割り当てが発生しません。

## <a name="adapters"></a>アダプター

`HttpRequest` と `HttpResponse` には、`Body`、`BodyReader`、`BodyWriter` プロパティを利用できます。 `Body` を別のストリームに設定すると、新しいアダプターのセットにより、各種類が別のものに自動的に適応します。 `HttpRequest.Body` を新しいストリームに設定した場合、`HttpRequest.BodyReader` は自動的に、`HttpRequest.Body` をラップする新しい `PipeReader` に設定されます。

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` は、ヘッダーが変更不可能であり、また `OnStarting` コールバックを実行することを示すために使います。 サーバーとして Kestrel を使う場合、`PipeReader` を使う前に `StartAsync` を呼び出すことで、`GetMemory` によって返されるメモリが、外部バッファーではなく Kestrel の内部 <xref:System.IO.Pipelines.Pipe> に属するよう保証できます。

## <a name="additional-resources"></a>その他の技術情報

* [.NET の System.IO.Pipelines](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
