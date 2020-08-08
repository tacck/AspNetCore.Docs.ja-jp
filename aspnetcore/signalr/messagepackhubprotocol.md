---
title: ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core に追加 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8e590c87f75d35cbafde1adbc87dea9c45eac92d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022551"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a>ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR

::: moniker range=">= aspnetcore-5.0"

この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。

## <a name="what-is-messagepack"></a>MessagePack とは

[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。 これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。 バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。 SignalRには、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。

## <a name="configure-messagepack-on-the-server"></a>サーバーでの MessagePack の構成

サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。 メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。

> [!NOTE]
> JSON は既定で有効になっています。 MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。 このデリゲートでは、 `SerializerOptions` プロパティを使用して MessagePack のシリアル化オプションを構成できます。 リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。 属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。 たとえば、を `.WithSecurity(MessagePackSecurity.UntrustedData)` 置換するときにを呼び出し `SerializerOptions` ます。

## <a name="configure-messagepack-on-the-client"></a>クライアントでの MessagePack の構成

> [!NOTE]
> JSON は、サポートされているクライアントに対して既定で有効になっています。 クライアントは、1つのプロトコルのみをサポートできます。 MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。

### <a name="net-client"></a>.NET クライアント

.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。 タグを使用して `<script>` 参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用する場合 `<script>` 、順序は重要です。 *msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。 *signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>MessagePack の特性

MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。

### <a name="messagepack-is-case-sensitive"></a>MessagePack では大文字と小文字が区別されます

MessagePack プロトコルでは大文字と小文字が区別されます。 たとえば、次の C# クラスについて考えてみます。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。 例:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。 この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時には DateTime. Kind は保持されません

MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。 その結果、日付を逆シリアル化するときに、がの場合、MessagePack ハブプロトコルは UTC 形式に変換され `DateTime.Kind` `DateTimeKind.Local` ます。それ以外の場合は、時刻に触れることなく、そのまま渡されます。 値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。 これらを受信時に UTC からローカル時刻に変換します。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue は、JavaScript の MessagePack ではサポートされていません

JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。 この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。 の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。 このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。 `DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。 MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前に" コンパイル環境での MessagePack のサポート

.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)ライブラリは、コード生成を使用してシリアル化を最適化します。 その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)を参照してください。 シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack での型チェックの方が厳しい場合

JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。 ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連リソース

* [Get Started (概要)](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。

## <a name="what-is-messagepack"></a>MessagePack とは

[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。 これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。 バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。 SignalRには、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。

## <a name="configure-messagepack-on-the-server"></a>サーバーでの MessagePack の構成

サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。 メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。

> [!NOTE]
> JSON は既定で有効になっています。 MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。 このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。 リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。 属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。 たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。 を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。 `MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。 `MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。 `MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>クライアントでの MessagePack の構成

> [!NOTE]
> JSON は、サポートされているクライアントに対して既定で有効になっています。 クライアントは、1つのプロトコルのみをサポートできます。 MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。

### <a name="net-client"></a>.NET クライアント

.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。 タグを使用して `<script>` 参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用する場合 `<script>` 、順序は重要です。 *msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。 *signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>MessagePack の特性

MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。

### <a name="messagepack-is-case-sensitive"></a>MessagePack では大文字と小文字が区別されます

MessagePack プロトコルでは大文字と小文字が区別されます。 たとえば、次の C# クラスについて考えてみます。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。 例:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。 この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時には DateTime. Kind は保持されません

MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。 その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。 現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。 これらを受信時に UTC からローカル時刻に変換します。

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue は、JavaScript の MessagePack ではサポートされていません

JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。 この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。 の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。 このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。 `DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。 MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前に" コンパイル環境での MessagePack のサポート

.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。 その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。 シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack での型チェックの方が厳しい場合

JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。 ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連リソース

* [Get Started (概要)](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。

## <a name="what-is-messagepack"></a>MessagePack とは

[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。 これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。 バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。 SignalRには、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。

## <a name="configure-messagepack-on-the-server"></a>サーバーでの MessagePack の構成

サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。 メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。

> [!NOTE]
> JSON は既定で有効になっています。 MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。 このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。 リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。 属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。 たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。 を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。 `MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。 `MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。 `MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>クライアントでの MessagePack の構成

> [!NOTE]
> JSON は、サポートされているクライアントに対して既定で有効になっています。 クライアントは、1つのプロトコルのみをサポートできます。 MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。

### <a name="net-client"></a>.NET クライアント

.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) ます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。 タグを使用して `<script>` 参照を作成します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。

> [!NOTE]
> 要素を使用する場合 `<script>` 、順序は重要です。 *msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。 *signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>MessagePack の特性

MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。

### <a name="messagepack-is-case-sensitive"></a>MessagePack では大文字と小文字が区別されます

MessagePack プロトコルでは大文字と小文字が区別されます。 たとえば、次の C# クラスについて考えてみます。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。 例:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。 この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>シリアル化/逆シリアル化時には DateTime. Kind は保持されません

MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。 その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。 現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。 これらを受信時に UTC からローカル時刻に変換します。

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MinValue は、JavaScript の MessagePack ではサポートされていません

JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。 この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。 の値は `DateTime.MinValue` 、 `January 1, 0001` 値でエンコードする必要があり `timestamp96` ます。 このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。 `DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。 MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前に" コンパイル環境での MessagePack のサポート

.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。 その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。 これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。 詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。 シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack での型チェックの方が厳しい場合

JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。 ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連リソース

* [Get Started (概要)](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)

::: moniker-end
