---
title: ASP.NET コアにSignalR対してメッセージ パック ハブ プロトコルを使用する
author: bradygaster
description: ASP.NET コアSignalRにメッセージ パック ハブ プロトコルを追加する 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277237"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>ASP.NET コアにSignalR対してメッセージ パック ハブ プロトコルを使用する

::: moniker range=">= aspnetcore-5.0"

この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。

## <a name="what-is-messagepack"></a>メッセージパックとは何ですか?

[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。 パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。 バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。 SignalRは、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。

## <a name="configure-messagepack-on-the-server"></a>サーバー上のメッセージ パックを構成する

サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。 メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。

> [!NOTE]
> JSON はデフォルトで有効になっています。 メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。 このデリゲートでは、この`SerializerOptions`プロパティを使用して MessagePack シリアル化オプションを構成できます。 リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。 属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。 たとえば、 を`.WithSecurity(MessagePackSecurity.UntrustedData)`置き換`SerializerOptions`えるときに呼び出す。

## <a name="configure-messagepack-on-the-client"></a>クライアントでメッセージ パックを構成する

> [!NOTE]
> JSON は、サポートされるクライアントに対してデフォルトで有効になっています。 クライアントは、1 つのプロトコルのみをサポートできます。 メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。

### <a name="net-client"></a>.NET クライアント

NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントのメッセージパックのサポートは[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)、npm パッケージによって提供されます。 コマンド シェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

ブラウザでは、ライブラリも`msgpack5`参照する必要があります。 タグを`<script>`使用して参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用`<script>`する場合、順序は重要です。 *signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。 *signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>メッセージパックの風変わり

メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。

### <a name="messagepack-is-case-sensitive"></a>メッセージパックでは大文字と小文字が区別されます。

メッセージ パック プロトコルでは、大文字と小文字が区別されます。 たとえば、次の C# クラスを考えてみましょう。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。 次に例を示します。

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前`camelCased`を使用しても、C# クラスに適切にバインドされません。 この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時に Kind が保持されない

MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。 その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコル`DateTime.Kind`は、それ以外の場合は`DateTimeKind.Local`、時刻に触れ、その時刻を元の状態で渡さない場合は、UTC 形式に変換されます。 値を処理する場合は`DateTime`、値を送信する前に UTC に変換することをお勧めします。 受信した時刻を UTC からローカル時刻に変換します。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript のメッセージ パックではサポートされていません。

JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。 の値`DateTime.MinValue`は`January 1, 0001`、`timestamp96`値でエンコードする必要があります。 このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。 JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。 MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前" コンパイル環境でのメッセージ パックのサポート

NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)ライブラリは、コード生成を使用してシリアル化を最適化します。 そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。 シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>メッセージパックでは型チェックがより厳格

JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。 ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。

## <a name="what-is-messagepack"></a>メッセージパックとは何ですか?

[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。 パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。 バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。 SignalRは、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。

## <a name="configure-messagepack-on-the-server"></a>サーバー上のメッセージ パックを構成する

サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。 メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。

> [!NOTE]
> JSON はデフォルトで有効になっています。 メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。 このデリゲートでは、この`FormatterResolvers`プロパティを使用して MessagePack シリアル化オプションを構成できます。 リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。 属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。 たとえば、静的プロパティを`MessagePackSecurity.Active`に設定`MessagePackSecurity.UntrustedData`します。 を設定`MessagePackSecurity.Active`するには[、1.9.x バージョンの MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。 1.9.x をインストール`MessagePack`するとSignalR、バージョンが使用するアップグレードが行われます。 `MessagePackSecurity.UntrustedData`に設定されていない場合`MessagePackSecurity.Active`、悪意のあるクライアントがサービス拒否を引き起こす可能性があります。 `Program.Main`に設定`MessagePackSecurity.Active`するコードを次に示します。

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>クライアントでメッセージ パックを構成する

> [!NOTE]
> JSON は、サポートされるクライアントに対してデフォルトで有効になっています。 クライアントは、1 つのプロトコルのみをサポートできます。 メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。

### <a name="net-client"></a>.NET クライアント

NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントのメッセージパックのサポートは[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)、npm パッケージによって提供されます。 コマンド シェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

ブラウザでは、ライブラリも`msgpack5`参照する必要があります。 タグを`<script>`使用して参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用`<script>`する場合、順序は重要です。 *signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。 *signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>メッセージパックの風変わり

メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。

### <a name="messagepack-is-case-sensitive"></a>メッセージパックでは大文字と小文字が区別されます。

メッセージ パック プロトコルでは、大文字と小文字が区別されます。 たとえば、次の C# クラスを考えてみましょう。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。 次に例を示します。

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前`camelCased`を使用しても、C# クラスに適切にバインドされません。 この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時に Kind が保持されない

MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。 その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコルは受信日付が UTC 形式であると想定します。 ローカル時間で値を`DateTime`使用する場合は、送信する前に UTC に変換することをお勧めします。 受信した時刻を UTC からローカル時刻に変換します。

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript のメッセージ パックではサポートされていません。

JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。 の値`DateTime.MinValue`は`January 1, 0001`、`timestamp96`値でエンコードする必要があります。 このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。 JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。 MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前" コンパイル環境でのメッセージ パックのサポート

NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。 そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>メッセージパックでは型チェックがより厳格

JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。 ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。

## <a name="what-is-messagepack"></a>メッセージパックとは何ですか?

[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。 パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。 バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。 SignalRは、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。

## <a name="configure-messagepack-on-the-server"></a>サーバー上のメッセージ パックを構成する

サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。 メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。

> [!NOTE]
> JSON はデフォルトで有効になっています。 メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。 このデリゲートでは、この`FormatterResolvers`プロパティを使用して MessagePack シリアル化オプションを構成できます。 リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。 属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。 たとえば、静的プロパティを`MessagePackSecurity.Active`に設定`MessagePackSecurity.UntrustedData`します。 を設定`MessagePackSecurity.Active`するには[、1.9.x バージョンの MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。 1.9.x をインストール`MessagePack`するとSignalR、バージョンが使用するアップグレードが行われます。 `MessagePackSecurity.UntrustedData`に設定されていない場合`MessagePackSecurity.Active`、悪意のあるクライアントがサービス拒否を引き起こす可能性があります。 `Program.Main`に設定`MessagePackSecurity.Active`するコードを次に示します。

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>クライアントでメッセージ パックを構成する

> [!NOTE]
> JSON は、サポートされるクライアントに対してデフォルトで有効になっています。 クライアントは、1 つのプロトコルのみをサポートできます。 メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。

### <a name="net-client"></a>.NET クライアント

NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントのメッセージパックのサポートは[@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack)、npm パッケージによって提供されます。 コマンド シェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @aspnet/signalr-protocol-msgpack
```

npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

ブラウザでは、ライブラリも`msgpack5`参照する必要があります。 タグを`<script>`使用して参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用`<script>`する場合、順序は重要です。 *signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。 *signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>メッセージパックの風変わり

メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。

### <a name="messagepack-is-case-sensitive"></a>メッセージパックでは大文字と小文字が区別されます。

メッセージ パック プロトコルでは、大文字と小文字が区別されます。 たとえば、次の C# クラスを考えてみましょう。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。 次に例を示します。

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前`camelCased`を使用しても、C# クラスに適切にバインドされません。 この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時に Kind が保持されない

MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。 その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコルは受信日付が UTC 形式であると想定します。 ローカル時間で値を`DateTime`使用する場合は、送信する前に UTC に変換することをお勧めします。 受信した時刻を UTC からローカル時刻に変換します。

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript のメッセージ パックではサポートされていません。

JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。 の値`DateTime.MinValue`は、`January 1, 0001``timestamp96`値でエンコードする必要があります。 このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。 JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。 MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前" コンパイル環境でのメッセージ パックのサポート

NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。 そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。 詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>メッセージパックでは型チェックがより厳格

JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。 ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end
