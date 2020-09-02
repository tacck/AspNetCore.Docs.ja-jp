---
title: .NET アプリの Protobuf メッセージを作成する
author: jamesnk
description: .NET アプリの Protobuf メッセージを作成する方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: b8149b79c1e7b204e52cc8595d1193b623bb0008
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945525"
---
# <a name="create-protobuf-messages-for-net-apps"></a>.NET アプリの Protobuf メッセージを作成する

作成者: [James Newton-King](https://twitter.com/jamesnk) および [Mark Rendle](https://twitter.com/markrendle)

gRPC によって、インターフェイス定義言語 (IDL) として [Protobuf](https://developers.google.com/protocol-buffers) が使用されます。 Protobuf IDL は、gRPC サービスによって送受信されるメッセージを指定するための言語に依存しない形式です。 Protobuf メッセージは、 *.proto* ファイルで定義されます。 このドキュメントでは、Protobuf の概念が .NET にどのようにマップされるかについて説明します。

## <a name="protobuf-messages"></a>Protobuf メッセージ

メッセージは、Protobuf の主なデータ転送オブジェクトです。 概念的には、.NET クラスに似ています。

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

上記のメッセージ定義によって、名前と値のペアとして 3 つのフィールドが指定されています。 .NET 型のプロパティと同様に、各フィールドには名前と型があります。 フィールドの型は、[Protobuf のスカラー値の型](#scalar-value-types) (`int32` など)、または別のメッセージにすることができます。

名前に加え、メッセージ定義の各フィールドには一意の番号があります。 フィールドの番号は、メッセージが Protobuf にシリアル化されるときにフィールドを特定するために使用されます。 小さい数をシリアル化する方が、フィールド名全体をシリアル化するよりも高速です。 フィールド番号によってフィールドが特定されるため、変更するときは注意が必要です。 Protobuf メッセージの変更の詳細については、「<xref:grpc/versioning>」を参照してください。

アプリがビルドされると、Protobuf ツールによって、 *.proto* ファイルから .NET 型が生成されます。 `Person` メッセージによって .NET クラスが生成されます。

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Protobuf メッセージの詳細については、[Protobuf の言語ガイド](https://developers.google.com/protocol-buffers/docs/proto3#simple)を参照してください。

## <a name="scalar-value-types"></a>スカラー値の型

Protobuf では、さまざまなネイティブ スカラー値の型がサポートされます。 次の表に、それらすべておよび同等の C# 型の一覧を示します。

| Protobuf 型 | C# 型      |
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

### <a name="dates-and-times"></a>日付と時刻

ネイティブ スカラー型では、NET の <xref:System.DateTimeOffset>、<xref:System.DateTime>、<xref:System.TimeSpan> と同等の、日付と時刻の値は提供されません。 これらの型は、いくつかの Protobuf の "既知の型" 拡張機能を使用して指定できます。 これらの拡張機能によって、サポート対象のプラットフォーム全体で複雑なフィールド型に対するコード生成とランタイム サポートが提供されます。

次の表に日付と時刻の型を示します。

| .NET の種類 | Protobuf の既知の型 |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

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

C# クラスで生成されるプロパティは、.NET の日付と時刻の型ではありません。 このプロパティによって、`Google.Protobuf.WellKnownTypes` 名前空間の `Timestamp` および `Duration` クラスが使用されます。 これらのクラスには、`DateTimeOffset`、`DateTime`、および `TimeSpan` の間で変換を行うためのメソッドが用意されています。

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
> `Timestamp` 型は UTC 時刻で動作します。 `DateTimeOffset` 値では常に、オフセットが 0 となり、`DateTime.Kind` プロパティは常に `DateTimeKind.Utc` となります。

### <a name="nullable-types"></a>null 許容型

C# に対する Protobuf のコード生成には、ネイティブ型 (`int32` に対して `int` など) が使用されます。 そのため、値は常に含まれ、`null` にすることはできません。

C# コードで `int?` を使用するなど、明示的な `null` を必要とする値の場合、Protobuf の "既知の型" には、C# の null 許容型にコンパイルされるラッパーが含まれます。 これらを使用するには、次のコードのように、`wrappers.proto` を `.proto` ファイルにインポートします。

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Protobuf によって、生成されたメッセージ プロパティに対して .NET の null 許容型 (`int?` など) が使用されます。

次の表に、ラッパーの型および同等の C# 型の完全な一覧を示します。

| C# 型   | 既知の型のラッパー       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a>10 進数

Protobuf では、.NET の `decimal` 型はネイティブでサポートされません。サポートされるのは、`double` と `float` のみとなります。 Protobuf プロジェクトでは、標準の 10 進数型を既知の型に追加し、それをサポートする言語とフレームワークをプラットフォームでサポートする可能性について、現在議論されています。 まだ何も実装されていません。

.NET クライアントとサーバー間の安全なシリアル化に使用できる、`decimal` 型を表すメッセージ定義を作成することができます。 しかし、他のプラットフォームの開発者は、使用されている形式を理解し、独自の処理を実装する必要があります。

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Protobuf のカスタム 10 進数型の作成

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

`nanos` フィールドは、`0.999_999_999` から `-0.999_999_999` までの値を表します。 たとえば、`decimal` 値の `1.5m` は `{ units = 1, nanos = 500_000_000 }` として表されます。 これが、この例の `nanos` フィールドで `sfixed32` 型が使用されている理由です。これで、より大きな値の場合、`int32` よりも効率的にエンコードされるようになります。 `units` フィールドが負の場合、`nanos` フィールドも負にする必要があります。

> [!NOTE]
> `decimal` 値をバイト文字列としてエンコードするためのアルゴリズムは他にもいくつかありますが、このメッセージが他のどれよりも理解しやすいものです。 これらの値は、さまざまなプラットフォーム上のビッグエンディアンやリトルエンディアンの影響を受けません。

この型と BCL の `decimal` 型の間の変換は、このように C# で実装される場合があります。

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

## <a name="collections"></a>コレクション

### <a name="lists"></a>リスト

Protobuf のリストは、フィールドで `repeated` プレフィックス キーワードを使用して指定されます。 次の例は、リストを作成する方法を示しています。

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

生成されたコードでは、`repeated` フィールドが `Google.Protobuf.Collections.RepeatedField<T>` ジェネリック型で表されます。

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` は、<xref:System.Collections.Generic.IList%601> を実装します。 そのため、LINQ クエリを使用したり、配列またはリストに変換したりすることができます。 `RepeatedField<T>` プロパティにはパブリック setter はありません。 項目は、既存のコレクションに追加する必要があります。

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>辞書

.NET の <xref:System.Collections.Generic.IDictionary%602> 型は、Protobuf では `map<key_type, value_type>` を使用して表されます。

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

生成された .NET コードでは、`map` フィールドが `Google.Protobuf.Collections.MapField<TKey, TValue>` ジェネリック型によって表されます。 `MapField<TKey, TValue>` は、<xref:System.Collections.Generic.IDictionary%602> を実装します。 `repeated` プロパティと同様に、`map` プロパティにはパブリック setter がありません。 項目は、既存のコレクションに追加する必要があります。

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

## <a name="unstructured-and-conditional-messages"></a>非構造化および条件付きメッセージ

Protobuf はコントラクト優先のメッセージング形式であり、アプリのビルド時にそのアプリのメッセージを *.proto* ファイルで指定する必要があります。 高度なシナリオでは、Protobuf によって、条件付きおよび不明なメッセージをサポートするための言語機能と既知の型が提供されます。

### <a name="any"></a>Any

`Any` 型を使用すると、 *.proto* 定義がなくても、埋め込み型としてメッセージを使用できます。 `Any` 型を使用するには、`any.proto` をインポートします。

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

### <a name="oneof"></a>Oneof

`oneof` フィールドは言語機能です。 コンパイラによって、message クラスが生成されるときに `oneof` キーワードが処理されます。 `oneof` を使用して、`Person` または `Error` を返すことができる応答メッセージを指定する場合は、このようになります。

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

`oneof` セット内のフィールドには、メッセージの宣言全体で一意のフィールド番号が含まれている必要があります。

`oneof` を使用する場合、生成される C# コードには、どのフィールドが設定されているかを示す列挙型が含まれます。 その列挙型をテストして、どのフィールドが設定されているかを確認することができます。 設定されていないフィールドによって、例外がスローされるのではなく、`null` または既定値が返されます。

```csharp
var response = client.GetPersonAsync(new RequestMessage());

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

### <a name="value"></a>値

`Value` 型は、動的に型指定される値を表します。 `null`、数値、文字列、ブール値、値の辞書 (`Struct`)、または値のリスト (`ValueList`) のいずれかにすることができます。 `Value` は、上記の `oneof` 機能を使用する既知の型です。 `Value` 型を使用するには、`struct.proto` をインポートします。

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

`Value` を直接使用すると、冗長になる場合があります。 `Value` を使用する別の方法として、メッセージを JSON にマップするための Protobuf の組み込みサポートの利用があります。 Protobuf の `JsonFormatter` および `JsonWriter` 型は、任意の Protobuf メッセージと共に使用できます。 `Value` は、JSON との間で変換する場合に特に適しています。

これは、上記のコードと同等の JSON です。

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

## <a name="additional-resources"></a>その他の技術情報

* [Protobuf の言語ガイド](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
