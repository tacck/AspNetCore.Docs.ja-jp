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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="a33e5-103">ASP.NET コアにSignalR対してメッセージ パック ハブ プロトコルを使用する</span><span class="sxs-lookup"><span data-stu-id="a33e5-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a33e5-104">この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a33e5-105">メッセージパックとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="a33e5-105">What is MessagePack?</span></span>

<span data-ttu-id="a33e5-106">[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a33e5-107">パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a33e5-108">バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a33e5-109">は、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a33e5-110">サーバー上のメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="a33e5-111">サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a33e5-112">メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-113">JSON はデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-113">JSON is enabled by default.</span></span> <span data-ttu-id="a33e5-114">メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a33e5-115">MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a33e5-116">このデリゲートでは、この`SerializerOptions`プロパティを使用して MessagePack シリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a33e5-117">リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a33e5-118">属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a33e5-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a33e5-120">たとえば、 を`.WithSecurity(MessagePackSecurity.UntrustedData)`置き換`SerializerOptions`えるときに呼び出す。</span><span class="sxs-lookup"><span data-stu-id="a33e5-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a33e5-121">クライアントでメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-122">JSON は、サポートされるクライアントに対してデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a33e5-123">クライアントは、1 つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="a33e5-124">メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a33e5-125">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-125">.NET client</span></span>

<span data-ttu-id="a33e5-126">NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-127">この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a33e5-128">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-128">JavaScript client</span></span>

<span data-ttu-id="a33e5-129">JavaScript クライアントのメッセージパックのサポートは[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)、npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a33e5-130">コマンド シェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a33e5-131">npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a33e5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a33e5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a33e5-133">ブラウザでは、ライブラリも`msgpack5`参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a33e5-134">タグを`<script>`使用して参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a33e5-135">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-136">要素を使用`<script>`する場合、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a33e5-137">*signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a33e5-138">*signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a33e5-139">に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-140">現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a33e5-141">メッセージパックの風変わり</span><span class="sxs-lookup"><span data-stu-id="a33e5-141">MessagePack quirks</span></span>

<span data-ttu-id="a33e5-142">メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a33e5-143">メッセージパックでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a33e5-144">メッセージ パック プロトコルでは、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a33e5-145">たとえば、次の C# クラスを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a33e5-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a33e5-146">大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a33e5-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a33e5-148">名前`camelCased`を使用しても、C# クラスに適切にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a33e5-149">この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a33e5-150">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a33e5-151">シリアル化/逆シリアル化時に Kind が保持されない</span><span class="sxs-lookup"><span data-stu-id="a33e5-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a33e5-152">MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a33e5-153">その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコル`DateTime.Kind`は、それ以外の場合は`DateTimeKind.Local`、時刻に触れ、その時刻を元の状態で渡さない場合は、UTC 形式に変換されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="a33e5-154">値を処理する場合は`DateTime`、値を送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a33e5-155">受信した時刻を UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a33e5-156">JavaScript のメッセージ パックではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a33e5-157">JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR</span><span class="sxs-lookup"><span data-stu-id="a33e5-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a33e5-158">この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a33e5-159">の値`DateTime.MinValue`は`January 1, 0001`、`timestamp96`値でエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a33e5-160">このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a33e5-161">JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a33e5-162">通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a33e5-163">MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a33e5-164">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a33e5-165">"事前" コンパイル環境でのメッセージ パックのサポート</span><span class="sxs-lookup"><span data-stu-id="a33e5-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a33e5-166">NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a33e5-167">そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a33e5-168">これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a33e5-169">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="a33e5-170">シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a33e5-171">メッセージパックでは型チェックがより厳格</span><span class="sxs-lookup"><span data-stu-id="a33e5-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a33e5-172">JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a33e5-173">たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a33e5-174">ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a33e5-175">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a33e5-176">関連資料</span><span class="sxs-lookup"><span data-stu-id="a33e5-176">Related resources</span></span>

* [<span data-ttu-id="a33e5-177">開始するには</span><span class="sxs-lookup"><span data-stu-id="a33e5-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a33e5-178">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a33e5-179">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="a33e5-180">この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a33e5-181">メッセージパックとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="a33e5-181">What is MessagePack?</span></span>

<span data-ttu-id="a33e5-182">[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a33e5-183">パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a33e5-184">バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a33e5-185">は、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a33e5-186">サーバー上のメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="a33e5-187">サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a33e5-188">メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-189">JSON はデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-189">JSON is enabled by default.</span></span> <span data-ttu-id="a33e5-190">メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a33e5-191">MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a33e5-192">このデリゲートでは、この`FormatterResolvers`プロパティを使用して MessagePack シリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a33e5-193">リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a33e5-194">属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a33e5-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a33e5-196">たとえば、静的プロパティを`MessagePackSecurity.Active`に設定`MessagePackSecurity.UntrustedData`します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a33e5-197">を設定`MessagePackSecurity.Active`するには[、1.9.x バージョンの MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a33e5-198">1.9.x をインストール`MessagePack`するとSignalR、バージョンが使用するアップグレードが行われます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a33e5-199">`MessagePackSecurity.UntrustedData`に設定されていない場合`MessagePackSecurity.Active`、悪意のあるクライアントがサービス拒否を引き起こす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a33e5-200">`Program.Main`に設定`MessagePackSecurity.Active`するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a33e5-201">クライアントでメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-202">JSON は、サポートされるクライアントに対してデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a33e5-203">クライアントは、1 つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="a33e5-204">メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a33e5-205">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-205">.NET client</span></span>

<span data-ttu-id="a33e5-206">NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-207">この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a33e5-208">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-208">JavaScript client</span></span>

<span data-ttu-id="a33e5-209">JavaScript クライアントのメッセージパックのサポートは[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)、npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a33e5-210">コマンド シェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a33e5-211">npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a33e5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a33e5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a33e5-213">ブラウザでは、ライブラリも`msgpack5`参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a33e5-214">タグを`<script>`使用して参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a33e5-215">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-216">要素を使用`<script>`する場合、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a33e5-217">*signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a33e5-218">*signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a33e5-219">に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-220">現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a33e5-221">メッセージパックの風変わり</span><span class="sxs-lookup"><span data-stu-id="a33e5-221">MessagePack quirks</span></span>

<span data-ttu-id="a33e5-222">メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a33e5-223">メッセージパックでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a33e5-224">メッセージ パック プロトコルでは、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a33e5-225">たとえば、次の C# クラスを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a33e5-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a33e5-226">大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a33e5-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a33e5-228">名前`camelCased`を使用しても、C# クラスに適切にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a33e5-229">この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a33e5-230">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a33e5-231">シリアル化/逆シリアル化時に Kind が保持されない</span><span class="sxs-lookup"><span data-stu-id="a33e5-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a33e5-232">MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a33e5-233">その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコルは受信日付が UTC 形式であると想定します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a33e5-234">ローカル時間で値を`DateTime`使用する場合は、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a33e5-235">受信した時刻を UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a33e5-236">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a33e5-237">JavaScript のメッセージ パックではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a33e5-238">JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR</span><span class="sxs-lookup"><span data-stu-id="a33e5-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a33e5-239">この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a33e5-240">の値`DateTime.MinValue`は`January 1, 0001`、`timestamp96`値でエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a33e5-241">このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a33e5-242">JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a33e5-243">通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a33e5-244">MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a33e5-245">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a33e5-246">"事前" コンパイル環境でのメッセージ パックのサポート</span><span class="sxs-lookup"><span data-stu-id="a33e5-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a33e5-247">NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a33e5-248">そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a33e5-249">これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a33e5-250">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a33e5-251">シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a33e5-252">メッセージパックでは型チェックがより厳格</span><span class="sxs-lookup"><span data-stu-id="a33e5-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a33e5-253">JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a33e5-254">たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a33e5-255">ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a33e5-256">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a33e5-257">関連資料</span><span class="sxs-lookup"><span data-stu-id="a33e5-257">Related resources</span></span>

* [<span data-ttu-id="a33e5-258">開始するには</span><span class="sxs-lookup"><span data-stu-id="a33e5-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a33e5-259">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a33e5-260">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a33e5-261">この記事では、「[はじめに](xref:tutorials/signalr)」で説明されているトピックに精通している読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a33e5-262">メッセージパックとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="a33e5-262">What is MessagePack?</span></span>

<span data-ttu-id="a33e5-263">[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリ シリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a33e5-264">パフォーマンスと帯域幅が懸念される場合は[、JSON](https://www.json.org/)と比較して小さなメッセージを作成するので便利です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a33e5-265">バイナリ メッセージは、メッセージ パック パーサーを介してバイトが渡されない限り、ネットワーク トレースとログを調べるときは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a33e5-266">は、MessagePack 形式の組み込みサポートを備えており、クライアントとサーバーが使用する API を提供します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a33e5-267">サーバー上のメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="a33e5-268">サーバーでメッセージ パック ハブ プロトコルを有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`パッケージをアプリにインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a33e5-269">メソッドで`Startup.ConfigureServices`、`AddSignalR`呼び`AddMessagePackProtocol`出しに追加して、サーバーでの MessagePack サポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-270">JSON はデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-270">JSON is enabled by default.</span></span> <span data-ttu-id="a33e5-271">メッセージ パックを追加すると、JSON クライアントとメッセージ パック クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a33e5-272">MessagePack がデータをフォーマットする方法を`AddMessagePackProtocol`カスタマイズするには、オプションを構成するためのデリゲートを取得します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a33e5-273">このデリゲートでは、この`FormatterResolvers`プロパティを使用して MessagePack シリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a33e5-274">リゾルバーの動作の詳細については、メッセージ[パック-CSharp のメッセージパック](https://github.com/neuecc/MessagePack-CSharp)ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a33e5-275">属性は、シリアル化するオブジェクトで使用して、その処理方法を定義できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a33e5-276">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨されるパッチを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a33e5-277">たとえば、静的プロパティを`MessagePackSecurity.Active`に設定`MessagePackSecurity.UntrustedData`します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a33e5-278">を設定`MessagePackSecurity.Active`するには[、1.9.x バージョンの MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a33e5-279">1.9.x をインストール`MessagePack`するとSignalR、バージョンが使用するアップグレードが行われます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a33e5-280">`MessagePackSecurity.UntrustedData`に設定されていない場合`MessagePackSecurity.Active`、悪意のあるクライアントがサービス拒否を引き起こす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a33e5-281">`Program.Main`に設定`MessagePackSecurity.Active`するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a33e5-282">クライアントでメッセージ パックを構成する</span><span class="sxs-lookup"><span data-stu-id="a33e5-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-283">JSON は、サポートされるクライアントに対してデフォルトで有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a33e5-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a33e5-284">クライアントは、1 つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="a33e5-285">メッセージ パックのサポートを追加すると、以前に構成したプロトコルが置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a33e5-286">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-286">.NET client</span></span>

<span data-ttu-id="a33e5-287">NET クライアントでメッセージ パックを有効にするには、パッケージ`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`をインストールし`AddMessagePackProtocol`、`HubConnectionBuilder`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-288">この`AddMessagePackProtocol`呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a33e5-289">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-289">JavaScript client</span></span>

<span data-ttu-id="a33e5-290">JavaScript クライアントのメッセージパックのサポートは[@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack)、npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a33e5-291">コマンド シェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="a33e5-292">npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーを介して直接使用するか、次のファイルを参照してブラウザにインポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a33e5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a33e5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="a33e5-294">ブラウザでは、ライブラリも`msgpack5`参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a33e5-295">タグを`<script>`使用して参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a33e5-296">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a33e5-297">要素を使用`<script>`する場合、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a33e5-298">*signalr プロトコル-msgpack.js*が*msgpack5.js*より前に参照されている場合、MessagePack を使用して接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a33e5-299">*signalr.js*は *、シグナル・プロトコル・msgpack.js の*前にも必要です。</span><span class="sxs-lookup"><span data-stu-id="a33e5-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a33e5-300">に`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`追加`HubConnectionBuilder`すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a33e5-301">現時点では、JavaScript クライアント上のメッセージ パック プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a33e5-302">メッセージパックの風変わり</span><span class="sxs-lookup"><span data-stu-id="a33e5-302">MessagePack quirks</span></span>

<span data-ttu-id="a33e5-303">メッセージ パック ハブ プロトコルを使用する場合、いくつかの問題に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a33e5-304">メッセージパックでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a33e5-305">メッセージ パック プロトコルでは、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a33e5-306">たとえば、次の C# クラスを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a33e5-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a33e5-307">大文字と小文字は C# クラスと正確`PascalCased`に一致する必要があるため、JavaScript クライアントから送信する場合は、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a33e5-308">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a33e5-309">名前`camelCased`を使用しても、C# クラスに適切にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a33e5-310">この問題を回避するには、 属性`Key`を使用して、MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a33e5-311">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a33e5-312">シリアル化/逆シリアル化時に Kind が保持されない</span><span class="sxs-lookup"><span data-stu-id="a33e5-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a33e5-313">MessagePack プロトコルは、 の`Kind`値をエンコードする方法を`DateTime`提供しません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a33e5-314">その結果、日付を逆シリアル化する場合、メッセージ パック ハブ プロトコルは受信日付が UTC 形式であると想定します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a33e5-315">ローカル時間で値を`DateTime`使用する場合は、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a33e5-316">受信した時刻を UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a33e5-317">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a33e5-318">JavaScript のメッセージ パックではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a33e5-319">JavaScript クライアントで使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、メッセージ`timestamp96`パックの型をサポートしていません。 SignalR</span><span class="sxs-lookup"><span data-stu-id="a33e5-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a33e5-320">この型は、非常に大きな日付値 (過去の非常に早い時期または非常に遠い将来) をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a33e5-321">の値`DateTime.MinValue`は、`January 1, 0001``timestamp96`値でエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a33e5-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a33e5-322">このため、JavaScript`DateTime.MinValue`クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a33e5-323">JavaScript クライアントが受信すると`DateTime.MinValue`、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a33e5-324">通常、「`DateTime.MinValue`欠落」または`null`値をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a33e5-325">MessagePack でその値をエンコードする必要がある場合は、null`DateTime`許容値`DateTime?`( )`bool`を使用するか、日付が存在するかどうか示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a33e5-326">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a33e5-327">"事前" コンパイル環境でのメッセージ パックのサポート</span><span class="sxs-lookup"><span data-stu-id="a33e5-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a33e5-328">NET クライアントとサーバーで使用される[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a33e5-329">そのため、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="a33e5-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a33e5-330">これらの環境では、シリアライザー/デシリアライザー コードを "事前生成" することで、MessagePack を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a33e5-331">詳細については、[メッセージパック-CSharp のドキュメントを参照してください](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="a33e5-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a33e5-332">シリアライザーを事前に生成したら、 に`AddMessagePackProtocol`渡された構成デリゲートを使用して、それらを登録できます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a33e5-333">メッセージパックでは型チェックがより厳格</span><span class="sxs-lookup"><span data-stu-id="a33e5-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a33e5-334">JSON ハブ プロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="a33e5-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a33e5-335">たとえば、受信オブジェクトに数値 (`{ foo: 42 }`) のプロパティ値がある場合、.NET クラスのプロパティは型の値が`string`変換されます。</span><span class="sxs-lookup"><span data-stu-id="a33e5-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a33e5-336">ただし、MessagePack はこの変換を実行せず、サーバー側のログ (およびコンソール) で見られる例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a33e5-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a33e5-337">この制限の詳細については、「 GitHub 発行[aspnetSignalR/ #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a33e5-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a33e5-338">関連資料</span><span class="sxs-lookup"><span data-stu-id="a33e5-338">Related resources</span></span>

* [<span data-ttu-id="a33e5-339">開始するには</span><span class="sxs-lookup"><span data-stu-id="a33e5-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a33e5-340">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a33e5-341">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="a33e5-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
