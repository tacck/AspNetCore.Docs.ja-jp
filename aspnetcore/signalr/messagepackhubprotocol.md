---
title: ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core に追加 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ff78321cba11b1c91a12b5c777c505b4c9b85309
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408319"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="c0ba4-103">ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR</span><span class="sxs-lookup"><span data-stu-id="c0ba4-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c0ba4-104">この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c0ba4-105">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="c0ba4-105">What is MessagePack?</span></span>

<span data-ttu-id="c0ba4-106">[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c0ba4-107">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c0ba4-108">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c0ba4-109">には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c0ba4-110">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="c0ba4-111">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c0ba4-112">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-113">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-113">JSON is enabled by default.</span></span> <span data-ttu-id="c0ba4-114">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c0ba4-115">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c0ba4-116">このデリゲートでは、 `SerializerOptions` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c0ba4-117">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c0ba4-118">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c0ba4-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c0ba4-120">たとえば、を `.WithSecurity(MessagePackSecurity.UntrustedData)` 置換するときにを呼び出し `SerializerOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c0ba4-121">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-122">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c0ba4-123">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="c0ba4-124">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c0ba4-125">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-125">.NET client</span></span>

<span data-ttu-id="c0ba4-126">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-127">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c0ba4-128">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-128">JavaScript client</span></span>

<span data-ttu-id="c0ba4-129">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c0ba4-130">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c0ba4-131">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c0ba4-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c0ba4-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c0ba4-133">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c0ba4-134">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c0ba4-135">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-136">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c0ba4-137">*msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c0ba4-138">*signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c0ba4-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-140">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c0ba4-141">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="c0ba4-141">MessagePack quirks</span></span>

<span data-ttu-id="c0ba4-142">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c0ba4-143">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="c0ba4-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c0ba4-144">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c0ba4-145">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c0ba4-146">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c0ba4-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c0ba4-148">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c0ba4-149">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c0ba4-150">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c0ba4-151">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c0ba4-152">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c0ba4-153">その結果、日付を逆シリアル化するときに、がの場合、MessagePack ハブプロトコルは UTC 形式に変換され `DateTime.Kind` `DateTimeKind.Local` ます。それ以外の場合は、時刻に触れることなく、そのまま渡されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="c0ba4-154">値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c0ba4-155">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c0ba4-156">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c0ba4-157">JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c0ba4-158">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c0ba4-159">の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c0ba4-160">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c0ba4-161">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c0ba4-162">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c0ba4-163">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c0ba4-164">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c0ba4-165">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="c0ba4-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c0ba4-166">.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c0ba4-167">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c0ba4-168">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c0ba4-169">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="c0ba4-170">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c0ba4-171">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="c0ba4-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c0ba4-172">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c0ba4-173">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c0ba4-174">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c0ba4-175">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c0ba4-176">関連リソース</span><span class="sxs-lookup"><span data-stu-id="c0ba4-176">Related resources</span></span>

* [<span data-ttu-id="c0ba4-177">Get Started (概要)</span><span class="sxs-lookup"><span data-stu-id="c0ba4-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c0ba4-178">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c0ba4-179">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c0ba4-180">この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c0ba4-181">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="c0ba4-181">What is MessagePack?</span></span>

<span data-ttu-id="c0ba4-182">[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c0ba4-183">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c0ba4-184">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c0ba4-185">には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c0ba4-186">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="c0ba4-187">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c0ba4-188">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-189">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-189">JSON is enabled by default.</span></span> <span data-ttu-id="c0ba4-190">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c0ba4-191">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c0ba4-192">このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c0ba4-193">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c0ba4-194">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c0ba4-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c0ba4-196">たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c0ba4-197">を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c0ba4-198">`MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c0ba4-199">`MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c0ba4-200">`MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c0ba4-201">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-202">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c0ba4-203">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="c0ba4-204">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c0ba4-205">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-205">.NET client</span></span>

<span data-ttu-id="c0ba4-206">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-207">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c0ba4-208">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-208">JavaScript client</span></span>

<span data-ttu-id="c0ba4-209">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c0ba4-210">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c0ba4-211">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c0ba4-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c0ba4-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c0ba4-213">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c0ba4-214">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c0ba4-215">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-216">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c0ba4-217">*msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c0ba4-218">*signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c0ba4-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-220">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c0ba4-221">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="c0ba4-221">MessagePack quirks</span></span>

<span data-ttu-id="c0ba4-222">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c0ba4-223">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="c0ba4-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c0ba4-224">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c0ba4-225">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c0ba4-226">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c0ba4-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c0ba4-228">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c0ba4-229">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c0ba4-230">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c0ba4-231">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c0ba4-232">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c0ba4-233">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c0ba4-234">現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c0ba4-235">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c0ba4-236">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c0ba4-237">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c0ba4-238">JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c0ba4-239">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c0ba4-240">の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c0ba4-241">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c0ba4-242">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c0ba4-243">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c0ba4-244">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c0ba4-245">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c0ba4-246">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="c0ba4-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c0ba4-247">.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c0ba4-248">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c0ba4-249">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c0ba4-250">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c0ba4-251">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c0ba4-252">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="c0ba4-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c0ba4-253">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c0ba4-254">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c0ba4-255">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c0ba4-256">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c0ba4-257">関連リソース</span><span class="sxs-lookup"><span data-stu-id="c0ba4-257">Related resources</span></span>

* [<span data-ttu-id="c0ba4-258">Get Started (概要)</span><span class="sxs-lookup"><span data-stu-id="c0ba4-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c0ba4-259">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c0ba4-260">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0ba4-261">この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c0ba4-262">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="c0ba4-262">What is MessagePack?</span></span>

<span data-ttu-id="c0ba4-263">[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c0ba4-264">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c0ba4-265">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c0ba4-266">には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c0ba4-267">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="c0ba4-268">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c0ba4-269">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-270">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-270">JSON is enabled by default.</span></span> <span data-ttu-id="c0ba4-271">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c0ba4-272">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c0ba4-273">このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c0ba4-274">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c0ba4-275">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="c0ba4-276">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c0ba4-277">たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c0ba4-278">を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c0ba4-279">`MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c0ba4-280">`MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c0ba4-281">`MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c0ba4-282">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="c0ba4-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-283">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c0ba4-284">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="c0ba4-285">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c0ba4-286">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-286">.NET client</span></span>

<span data-ttu-id="c0ba4-287">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-288">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c0ba4-289">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-289">JavaScript client</span></span>

<span data-ttu-id="c0ba4-290">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c0ba4-291">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="c0ba4-292">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c0ba4-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c0ba4-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="c0ba4-294">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c0ba4-295">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c0ba4-296">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c0ba4-297">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c0ba4-298">*msgpack5.js*する前に*signalr-protocol-msgpack.js*が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c0ba4-299">*signalr-protocol-msgpack.js*する前に*signalr.js*も必要です。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c0ba4-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c0ba4-301">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c0ba4-302">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="c0ba4-302">MessagePack quirks</span></span>

<span data-ttu-id="c0ba4-303">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c0ba4-304">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="c0ba4-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c0ba4-305">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c0ba4-306">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c0ba4-307">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c0ba4-308">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c0ba4-309">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c0ba4-310">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c0ba4-311">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c0ba4-312">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c0ba4-313">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c0ba4-314">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c0ba4-315">現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c0ba4-316">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c0ba4-317">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c0ba4-318">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="c0ba4-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c0ba4-319">JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c0ba4-320">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c0ba4-321">の値は `DateTime.MinValue` 、 `January 1, 0001` 値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c0ba4-322">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c0ba4-323">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c0ba4-324">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c0ba4-325">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c0ba4-326">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c0ba4-327">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="c0ba4-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c0ba4-328">.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c0ba4-329">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c0ba4-330">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c0ba4-331">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c0ba4-332">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c0ba4-333">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="c0ba4-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c0ba4-334">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c0ba4-335">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c0ba4-336">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c0ba4-337">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0ba4-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c0ba4-338">関連リソース</span><span class="sxs-lookup"><span data-stu-id="c0ba4-338">Related resources</span></span>

* [<span data-ttu-id="c0ba4-339">Get Started (概要)</span><span class="sxs-lookup"><span data-stu-id="c0ba4-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c0ba4-340">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c0ba4-341">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c0ba4-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
