---
title: .NET アプリの Protobuf メッセージを作成する
author: jamesnk
description: .NET アプリの Protobuf メッセージを作成する方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: b70a5ee00405eecfce900b86dc631a54682dce1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058897"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="fc56f-103">.NET アプリの Protobuf メッセージを作成する</span><span class="sxs-lookup"><span data-stu-id="fc56f-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="fc56f-104">作成者: [James Newton-King](https://twitter.com/jamesnk) および [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="fc56f-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="fc56f-105">gRPC によって、インターフェイス定義言語 (IDL) として [Protobuf](https://developers.google.com/protocol-buffers) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="fc56f-106">Protobuf IDL は、gRPC サービスによって送受信されるメッセージを指定するための言語に依存しない形式です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="fc56f-107">Protobuf メッセージは、 `.proto` ファイルで定義されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="fc56f-108">このドキュメントでは、Protobuf の概念が .NET にどのようにマップされるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="fc56f-109">Protobuf メッセージ</span><span class="sxs-lookup"><span data-stu-id="fc56f-109">Protobuf messages</span></span>

<span data-ttu-id="fc56f-110">メッセージは、Protobuf の主なデータ転送オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="fc56f-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="fc56f-111">概念的には、.NET クラスに似ています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="fc56f-112">上記のメッセージ定義によって、名前と値のペアとして 3 つのフィールドが指定されています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="fc56f-113">.NET 型のプロパティと同様に、各フィールドには名前と型があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="fc56f-114">フィールドの型は、[Protobuf のスカラー値の型](#scalar-value-types) (`int32` など)、または別のメッセージにすることができます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="fc56f-115">名前に加え、メッセージ定義の各フィールドには一意の番号があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="fc56f-116">フィールドの番号は、メッセージが Protobuf にシリアル化されるときにフィールドを特定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="fc56f-117">小さい数をシリアル化する方が、フィールド名全体をシリアル化するよりも高速です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="fc56f-118">フィールド番号によってフィールドが特定されるため、変更するときは注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="fc56f-119">Protobuf メッセージの変更の詳細については、「<xref:grpc/versioning>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc56f-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="fc56f-120">アプリがビルドされると、Protobuf ツールによって、 `.proto` ファイルから .NET 型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-120">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="fc56f-121">`Person` メッセージによって .NET クラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="fc56f-122">Protobuf メッセージの詳細については、[Protobuf の言語ガイド](https://developers.google.com/protocol-buffers/docs/proto3#simple)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fc56f-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="fc56f-123">スカラー値の型</span><span class="sxs-lookup"><span data-stu-id="fc56f-123">Scalar Value Types</span></span>

<span data-ttu-id="fc56f-124">Protobuf では、さまざまなネイティブ スカラー値の型がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="fc56f-125">次の表に、それらすべておよび同等の C# 型の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="fc56f-126">Protobuf 型</span><span class="sxs-lookup"><span data-stu-id="fc56f-126">Protobuf type</span></span> | <span data-ttu-id="fc56f-127">C# 型</span><span class="sxs-lookup"><span data-stu-id="fc56f-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

<span data-ttu-id="fc56f-128">スカラー値には常に既定値があり、`null` に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-128">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="fc56f-129">この制約には、C# クラスである `string` および `ByteString` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-129">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="fc56f-130">`string` 既定値は空の文字列値で、`ByteString` 既定値は空のバイト値です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-130">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="fc56f-131">これらを `null` に設定しようとすると、エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-131">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="fc56f-132">[null 許容型のラッパー](#nullable-types)を使用して null 値をサポートできます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-132">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="fc56f-133">日付と時刻</span><span class="sxs-lookup"><span data-stu-id="fc56f-133">Dates and times</span></span>

<span data-ttu-id="fc56f-134">ネイティブ スカラー型では、NET の <xref:System.DateTimeOffset>、<xref:System.DateTime>、<xref:System.TimeSpan> と同等の、日付と時刻の値は提供されません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-134">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="fc56f-135">これらの型は、いくつかの Protobuf の " *既知の型* " 拡張機能を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-135">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="fc56f-136">これらの拡張機能によって、サポート対象のプラットフォーム全体で複雑なフィールド型に対するコード生成とランタイム サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-136">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="fc56f-137">次の表に日付と時刻の型を示します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-137">The following table shows the date and time types:</span></span>

| <span data-ttu-id="fc56f-138">.NET の種類</span><span class="sxs-lookup"><span data-stu-id="fc56f-138">.NET type</span></span>        | <span data-ttu-id="fc56f-139">Protobuf の既知の型</span><span class="sxs-lookup"><span data-stu-id="fc56f-139">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="fc56f-140">C# クラスで生成されるプロパティは、.NET の日付と時刻の型ではありません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-140">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="fc56f-141">このプロパティによって、`Google.Protobuf.WellKnownTypes` 名前空間の `Timestamp` および `Duration` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-141">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="fc56f-142">これらのクラスには、`DateTimeOffset`、`DateTime`、および `TimeSpan` の間で変換を行うためのメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-142">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="fc56f-143">`Timestamp` 型は UTC 時刻で動作します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-143">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="fc56f-144">`DateTimeOffset` 値では常に、オフセットが 0 となり、`DateTime.Kind` プロパティは常に `DateTimeKind.Utc` となります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-144">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="fc56f-145">null 許容型</span><span class="sxs-lookup"><span data-stu-id="fc56f-145">Nullable types</span></span>

<span data-ttu-id="fc56f-146">C# に対する Protobuf のコード生成には、ネイティブ型 (`int32` に対して `int` など) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-146">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="fc56f-147">そのため、値は常に含まれ、`null` にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-147">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="fc56f-148">C# コードで `int?` を使用するなど、明示的な `null` を必要とする値の場合、Protobuf の "既知の型" には、C# の null 許容型にコンパイルされるラッパーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-148">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="fc56f-149">これらを使用するには、次のコードのように、`wrappers.proto` を `.proto` ファイルにインポートします。</span><span class="sxs-lookup"><span data-stu-id="fc56f-149">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="fc56f-150">`wrappers.proto` 型は、生成されたプロパティでは公開されません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-150">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="fc56f-151">Protobuf により、C# メッセージ内の適切な .NET null 許容型に自動的にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-151">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="fc56f-152">たとえば、`google.protobuf.Int32Value` フィールドにより、`int?` プロパティが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-152">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="fc56f-153">`string` や `ByteString` などの参照型プロパティは変更されませんが、`null` をエラーなしで割り当てることはできます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-153">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="fc56f-154">次の表に、ラッパーの型および同等の C# 型の完全な一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-154">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="fc56f-155">C# 型</span><span class="sxs-lookup"><span data-stu-id="fc56f-155">C# type</span></span>      | <span data-ttu-id="fc56f-156">既知の型のラッパー</span><span class="sxs-lookup"><span data-stu-id="fc56f-156">Well-Known Type wrapper</span></span>       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a><span data-ttu-id="fc56f-157">バイト</span><span class="sxs-lookup"><span data-stu-id="fc56f-157">Bytes</span></span>

<span data-ttu-id="fc56f-158">バイナリ ペイロードは、`bytes` スカラー値型の Protobuf でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-158">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="fc56f-159">C# で生成されたプロパティにより、プロパティの型として `ByteString` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-159">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="fc56f-160">`ByteString.CopyFrom(byte[] data)` を使用して、バイト配列から新しいインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-160">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="fc56f-161">`ByteString` データは、`ByteString.Span` または `ByteString.Memory` を使用して直接アクセスされます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-161">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="fc56f-162">または、`ByteString.ToByteArray()` を呼び出して、インスタンスをバイト配列に変換します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-162">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="fc56f-163">10 進数</span><span class="sxs-lookup"><span data-stu-id="fc56f-163">Decimals</span></span>

<span data-ttu-id="fc56f-164">Protobuf では、.NET の `decimal` 型はネイティブでサポートされません。サポートされるのは、`double` と `float` のみとなります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-164">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="fc56f-165">Protobuf プロジェクトでは、標準の 10 進数型を既知の型に追加し、それをサポートする言語とフレームワークをプラットフォームでサポートする可能性について、現在議論されています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-165">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="fc56f-166">まだ何も実装されていません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-166">Nothing has been implemented yet.</span></span>

<span data-ttu-id="fc56f-167">.NET クライアントとサーバー間の安全なシリアル化に使用できる、`decimal` 型を表すメッセージ定義を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-167">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="fc56f-168">しかし、他のプラットフォームの開発者は、使用されている形式を理解し、独自の処理を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-168">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="fc56f-169">Protobuf のカスタム 10 進数型の作成</span><span class="sxs-lookup"><span data-stu-id="fc56f-169">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="fc56f-170">`nanos` フィールドは、`0.999_999_999` から `-0.999_999_999` までの値を表します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-170">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="fc56f-171">たとえば、`decimal` 値の `1.5m` は `{ units = 1, nanos = 500_000_000 }` として表されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-171">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="fc56f-172">これが、この例の `nanos` フィールドで `sfixed32` 型が使用されている理由です。これで、より大きな値の場合、`int32` よりも効率的にエンコードされるようになります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-172">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="fc56f-173">`units` フィールドが負の場合、`nanos` フィールドも負にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-173">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="fc56f-174">`decimal` 値をバイト文字列としてエンコードするためのアルゴリズムは他にもいくつかありますが、このメッセージが他のどれよりも理解しやすいものです。</span><span class="sxs-lookup"><span data-stu-id="fc56f-174">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="fc56f-175">これらの値は、さまざまなプラットフォーム上のビッグエンディアンやリトルエンディアンの影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-175">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="fc56f-176">この型と BCL の `decimal` 型の間の変換は、このように C# で実装される場合があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-176">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="fc56f-177">コレクション</span><span class="sxs-lookup"><span data-stu-id="fc56f-177">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="fc56f-178">リスト</span><span class="sxs-lookup"><span data-stu-id="fc56f-178">Lists</span></span>

<span data-ttu-id="fc56f-179">Protobuf のリストは、フィールドで `repeated` プレフィックス キーワードを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-179">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="fc56f-180">次の例は、リストを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-180">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="fc56f-181">生成されたコードでは、`repeated` フィールドが `Google.Protobuf.Collections.RepeatedField<T>` ジェネリック型で表されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-181">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="fc56f-182">`RepeatedField<T>` は、<xref:System.Collections.Generic.IList%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-182">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="fc56f-183">そのため、LINQ クエリを使用したり、配列またはリストに変換したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-183">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="fc56f-184">`RepeatedField<T>` プロパティにはパブリック setter はありません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-184">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="fc56f-185">項目は、既存のコレクションに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-185">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="fc56f-186">辞書</span><span class="sxs-lookup"><span data-stu-id="fc56f-186">Dictionaries</span></span>

<span data-ttu-id="fc56f-187">.NET の <xref:System.Collections.Generic.IDictionary%602> 型は、Protobuf では `map<key_type, value_type>` を使用して表されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-187">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="fc56f-188">生成された .NET コードでは、`map` フィールドが `Google.Protobuf.Collections.MapField<TKey, TValue>` ジェネリック型によって表されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-188">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="fc56f-189">`MapField<TKey, TValue>` は、<xref:System.Collections.Generic.IDictionary%602> を実装します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-189">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="fc56f-190">`repeated` プロパティと同様に、`map` プロパティにはパブリック setter がありません。</span><span class="sxs-lookup"><span data-stu-id="fc56f-190">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="fc56f-191">項目は、既存のコレクションに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-191">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="fc56f-192">非構造化および条件付きメッセージ</span><span class="sxs-lookup"><span data-stu-id="fc56f-192">Unstructured and conditional messages</span></span>

<span data-ttu-id="fc56f-193">Protobuf は、コントラクト優先のメッセージング形式です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-193">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="fc56f-194">アプリがビルドされるときに、アプリのメッセージ (そのフィールドや型を含む) を `.proto` ファイルで指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-194">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="fc56f-195">Protobuf のコントラクト優先設計は、メッセージ コンテンツを適用する場合に適していますが、次のような厳密なコントラクトが必要ではないシナリオを制限できます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-195">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="fc56f-196">ペイロードが不明なメッセージ。</span><span class="sxs-lookup"><span data-stu-id="fc56f-196">Messages with unknown payloads.</span></span> <span data-ttu-id="fc56f-197">たとえば、何らかのメッセージが含まれる可能性があるフィールドを含むメッセージです。</span><span class="sxs-lookup"><span data-stu-id="fc56f-197">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="fc56f-198">条件付きメッセージ。</span><span class="sxs-lookup"><span data-stu-id="fc56f-198">Conditional messages.</span></span> <span data-ttu-id="fc56f-199">たとえば、gRPC サービスから返されたメッセージは、結果が成功またはエラーになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-199">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="fc56f-200">動的な値。</span><span class="sxs-lookup"><span data-stu-id="fc56f-200">Dynamic values.</span></span> <span data-ttu-id="fc56f-201">たとえば、JSON のような、値の非構造化コレクションが含まれるフィールドを含むメッセージ。</span><span class="sxs-lookup"><span data-stu-id="fc56f-201">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="fc56f-202">Protobuf には、これらのシナリオをサポートするための言語の機能と型が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-202">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="fc56f-203">Any</span><span class="sxs-lookup"><span data-stu-id="fc56f-203">Any</span></span>

<span data-ttu-id="fc56f-204">`Any` 型を使用すると、 `.proto` 定義がなくても、埋め込み型としてメッセージを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-204">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="fc56f-205">`Any` 型を使用するには、`any.proto` をインポートします。</span><span class="sxs-lookup"><span data-stu-id="fc56f-205">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="fc56f-206">Oneof</span><span class="sxs-lookup"><span data-stu-id="fc56f-206">Oneof</span></span>

<span data-ttu-id="fc56f-207">`oneof` フィールドは言語機能です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-207">`oneof` fields are a language feature.</span></span> <span data-ttu-id="fc56f-208">コンパイラによって、message クラスが生成されるときに `oneof` キーワードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-208">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="fc56f-209">`oneof` を使用して、`Person` または `Error` を返すことができる応答メッセージを指定する場合は、このようになります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-209">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="fc56f-210">`oneof` セット内のフィールドには、メッセージの宣言全体で一意のフィールド番号が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-210">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="fc56f-211">`oneof` を使用する場合、生成される C# コードには、どのフィールドが設定されているかを示す列挙型が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-211">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="fc56f-212">その列挙型をテストして、どのフィールドが設定されているかを確認することができます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-212">You can test the enum to find which field is set.</span></span> <span data-ttu-id="fc56f-213">設定されていないフィールドによって、例外がスローされるのではなく、`null` または既定値が返されます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-213">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="fc56f-214">値</span><span class="sxs-lookup"><span data-stu-id="fc56f-214">Value</span></span>

<span data-ttu-id="fc56f-215">`Value` 型は、動的に型指定される値を表します。</span><span class="sxs-lookup"><span data-stu-id="fc56f-215">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="fc56f-216">`null`、数値、文字列、ブール値、値の辞書 (`Struct`)、または値のリスト (`ValueList`) のいずれかにすることができます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-216">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="fc56f-217">`Value` は、上記の `oneof` 機能を使用する Protobuf の既知の型です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-217">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="fc56f-218">`Value` 型を使用するには、`struct.proto` をインポートします。</span><span class="sxs-lookup"><span data-stu-id="fc56f-218">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="fc56f-219">`Value` を直接使用すると、冗長になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-219">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="fc56f-220">`Value` を使用する別の方法として、メッセージを JSON にマップするための Protobuf の組み込みサポートの利用があります。</span><span class="sxs-lookup"><span data-stu-id="fc56f-220">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="fc56f-221">Protobuf の `JsonFormatter` および `JsonWriter` 型は、任意の Protobuf メッセージと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc56f-221">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="fc56f-222">`Value` は、JSON との間で変換する場合に特に適しています。</span><span class="sxs-lookup"><span data-stu-id="fc56f-222">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="fc56f-223">これは、上記のコードと同等の JSON です。</span><span class="sxs-lookup"><span data-stu-id="fc56f-223">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="fc56f-224">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fc56f-224">Additional resources</span></span>

* [<span data-ttu-id="fc56f-225">Protobuf の言語ガイド</span><span class="sxs-lookup"><span data-stu-id="fc56f-225">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
