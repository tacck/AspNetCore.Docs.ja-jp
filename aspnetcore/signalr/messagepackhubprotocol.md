---
title: ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core に追加 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- appsettings.json
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024692"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="fd424-103">ASP.NET Core には、の MessagePack ハブプロトコルを使用します。 SignalR</span><span class="sxs-lookup"><span data-stu-id="fd424-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fd424-104">この記事では、「 [作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="fd424-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fd424-105">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="fd424-105">What is MessagePack?</span></span>

<span data-ttu-id="fd424-106">[Messagepack](https://msgpack.org/index.html) は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="fd424-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fd424-107">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fd424-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fd424-108">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="fd424-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="fd424-109">SignalR には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fd424-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fd424-110">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="fd424-111">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fd424-112">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="fd424-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-113">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-113">JSON is enabled by default.</span></span> <span data-ttu-id="fd424-114">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="fd424-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fd424-115">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fd424-116">このデリゲートでは、 `SerializerOptions` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fd424-117">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fd424-118">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="fd424-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fd424-120">たとえば、を `.WithSecurity(MessagePackSecurity.UntrustedData)` 置換するときにを呼び出し `SerializerOptions` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fd424-121">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-122">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fd424-123">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="fd424-124">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="fd424-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fd424-125">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-125">.NET client</span></span>

<span data-ttu-id="fd424-126">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fd424-127">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fd424-128">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-128">JavaScript client</span></span>

<span data-ttu-id="fd424-129">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fd424-130">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="fd424-131">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fd424-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fd424-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="fd424-133">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fd424-134">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="fd424-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fd424-135">ライブラリは *node_modules\msgpack5\dist\msgpack5.js* にあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-136">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fd424-137">*msgpack5.js* する前に *signalr-protocol-msgpack.js* が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="fd424-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fd424-138">*signalr-protocol-msgpack.js* する前に *signalr.js* も必要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fd424-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fd424-140">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="fd424-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="fd424-141">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-141">Java client</span></span>

<span data-ttu-id="fd424-142">Java で MessagePack を有効にするには、パッケージをインストールし `com.microsoft.signalr.messagepack` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="fd424-143">Gradle を使用する場合は、 `dependencies` *Gradle* ファイルのセクションに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="fd424-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="fd424-144">Maven を使用する場合は、 `<dependencies>` *pom.xml* ファイルの要素内に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="fd424-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="fd424-145">`withHubProtocol(new MessagePackHubProtocol())`でを呼び出し `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="fd424-146">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="fd424-146">MessagePack quirks</span></span>

<span data-ttu-id="fd424-147">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fd424-148">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="fd424-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fd424-149">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fd424-150">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="fd424-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fd424-151">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fd424-152">例:</span><span class="sxs-lookup"><span data-stu-id="fd424-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fd424-153">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fd424-154">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fd424-155">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fd424-156">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="fd424-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fd424-157">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fd424-158">その結果、日付を逆シリアル化するときに、がの場合、MessagePack ハブプロトコルは UTC 形式に変換され `DateTime.Kind` `DateTimeKind.Local` ます。それ以外の場合は、時刻に触れることなく、そのまま渡されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="fd424-159">値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fd424-160">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="fd424-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fd424-161">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="fd424-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fd424-162">JavaScript クライアントによって使用される [msgpack5](https://github.com/mcollina/msgpack5) ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fd424-163">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="fd424-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fd424-164">の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fd424-165">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="fd424-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fd424-166">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fd424-167">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fd424-168">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="fd424-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fd424-169">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fd424-170">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="fd424-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fd424-171">.NET クライアントとサーバーで使用される [Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="fd424-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fd424-172">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fd424-173">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fd424-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fd424-174">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="fd424-175">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fd424-176">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="fd424-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fd424-177">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="fd424-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fd424-178">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fd424-179">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fd424-180">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="fd424-181">Java での文字と文字列</span><span class="sxs-lookup"><span data-stu-id="fd424-181">Chars and Strings in Java</span></span>

<span data-ttu-id="fd424-182">Java クライアントでは、 `char` オブジェクトは1文字のオブジェクトとしてシリアル化され `String` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="fd424-183">これは、オブジェクトとしてシリアル化する C# および JavaScript クライアントとは対照的です `short` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="fd424-184">MessagePack の仕様自体は、オブジェクトの動作を定義していない `char` ため、ライブラリの作成者がオブジェクトをシリアル化する方法を決定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="fd424-185">クライアント間の動作の違いは、実装に使用したライブラリの結果です。</span><span class="sxs-lookup"><span data-stu-id="fd424-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="fd424-186">関連リソース</span><span class="sxs-lookup"><span data-stu-id="fd424-186">Related resources</span></span>

* [<span data-ttu-id="fd424-187">開始するには</span><span class="sxs-lookup"><span data-stu-id="fd424-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fd424-188">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fd424-189">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="fd424-190">この記事では、「 [作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="fd424-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fd424-191">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="fd424-191">What is MessagePack?</span></span>

<span data-ttu-id="fd424-192">[Messagepack](https://msgpack.org/index.html) は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="fd424-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fd424-193">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fd424-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fd424-194">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="fd424-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="fd424-195">SignalR には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fd424-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fd424-196">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="fd424-197">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fd424-198">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="fd424-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-199">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-199">JSON is enabled by default.</span></span> <span data-ttu-id="fd424-200">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="fd424-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fd424-201">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fd424-202">このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fd424-203">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fd424-204">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="fd424-205">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fd424-206">たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="fd424-207">を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="fd424-208">`MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。</span><span class="sxs-lookup"><span data-stu-id="fd424-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="fd424-209">`MessagePack` バージョン2.x では、互換性に影響する変更が導入され、バージョン3.1 以前と互換性がありません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="fd424-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="fd424-210">`MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="fd424-211">`MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fd424-212">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-213">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fd424-214">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="fd424-215">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="fd424-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fd424-216">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-216">.NET client</span></span>

<span data-ttu-id="fd424-217">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fd424-218">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fd424-219">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-219">JavaScript client</span></span>

<span data-ttu-id="fd424-220">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fd424-221">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="fd424-222">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fd424-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fd424-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="fd424-224">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fd424-225">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="fd424-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fd424-226">ライブラリは *node_modules\msgpack5\dist\msgpack5.js* にあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-227">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fd424-228">*msgpack5.js* する前に *signalr-protocol-msgpack.js* が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="fd424-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fd424-229">*signalr-protocol-msgpack.js* する前に *signalr.js* も必要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fd424-230">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fd424-231">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="fd424-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fd424-232">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="fd424-232">MessagePack quirks</span></span>

<span data-ttu-id="fd424-233">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fd424-234">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="fd424-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fd424-235">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fd424-236">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="fd424-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fd424-237">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fd424-238">例:</span><span class="sxs-lookup"><span data-stu-id="fd424-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fd424-239">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fd424-240">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fd424-241">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fd424-242">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="fd424-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fd424-243">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fd424-244">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fd424-245">現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fd424-246">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="fd424-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fd424-247">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fd424-248">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="fd424-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fd424-249">JavaScript クライアントによって使用される [msgpack5](https://github.com/mcollina/msgpack5) ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fd424-250">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="fd424-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fd424-251">の値 `DateTime.MinValue` はで `January 1, 0001` あり、値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fd424-252">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="fd424-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fd424-253">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fd424-254">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fd424-255">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="fd424-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fd424-256">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fd424-257">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="fd424-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fd424-258">.NET クライアントとサーバーで使用される [Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="fd424-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fd424-259">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fd424-260">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fd424-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fd424-261">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fd424-262">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fd424-263">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="fd424-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fd424-264">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="fd424-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fd424-265">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fd424-266">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fd424-267">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fd424-268">関連リソース</span><span class="sxs-lookup"><span data-stu-id="fd424-268">Related resources</span></span>

* [<span data-ttu-id="fd424-269">開始するには</span><span class="sxs-lookup"><span data-stu-id="fd424-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fd424-270">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fd424-271">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd424-272">この記事では、「 [作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="fd424-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fd424-273">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="fd424-273">What is MessagePack?</span></span>

<span data-ttu-id="fd424-274">[Messagepack](https://msgpack.org/index.html) は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="fd424-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="fd424-275">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="fd424-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fd424-276">バイナリメッセージは、ネットワークトレースとログを参照するときに、MessagePack パーサーを通じてバイトが渡されない限り、読み取ることができません。</span><span class="sxs-lookup"><span data-stu-id="fd424-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="fd424-277">SignalR には、MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="fd424-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fd424-278">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="fd424-279">サーバーで MessagePack ハブプロトコルを有効にするには、 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` アプリにパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fd424-280">メソッドで `Startup.ConfigureServices` 、を呼び出しに追加して、 `AddMessagePackProtocol` `AddSignalR` サーバーで messagepack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="fd424-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-281">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-281">JSON is enabled by default.</span></span> <span data-ttu-id="fd424-282">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="fd424-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fd424-283">MessagePack によるデータの書式設定方法をカスタマイズするために、は `AddMessagePackProtocol` オプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fd424-284">このデリゲートでは、 `FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fd424-285">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fd424-286">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="fd424-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="fd424-287">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="fd424-288">たとえば、 `MessagePackSecurity.Active` 静的プロパティをに設定し `MessagePackSecurity.UntrustedData` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="fd424-289">を設定するには、 `MessagePackSecurity.Active` [バージョンの messagepack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="fd424-290">`MessagePack`1.9. x のアップグレードのバージョンが SignalR 使用する。</span><span class="sxs-lookup"><span data-stu-id="fd424-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="fd424-291">`MessagePackSecurity.Active`がに設定されていない場合 `MessagePackSecurity.UntrustedData` 、悪意のあるクライアントによってサービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="fd424-292">`MessagePackSecurity.Active`次の `Program.Main` コードに示すように、でを設定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fd424-293">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="fd424-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-294">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="fd424-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fd424-295">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="fd424-296">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="fd424-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fd424-297">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-297">.NET client</span></span>

<span data-ttu-id="fd424-298">.NET クライアントで MessagePack を有効にするには、パッケージをインストール `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` し、でを呼び出し `AddMessagePackProtocol` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fd424-299">この `AddMessagePackProtocol` 呼び出しは、サーバーと同じようにオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd424-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fd424-300">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-300">JavaScript client</span></span>

<span data-ttu-id="fd424-301">JavaScript クライアントの MessagePack のサポートは、npm パッケージによって提供され [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="fd424-302">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fd424-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="fd424-303">Npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="fd424-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="fd424-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fd424-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="fd424-305">ブラウザーで `msgpack5` も、ライブラリを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fd424-306">タグを使用して `<script>` 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="fd424-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fd424-307">ライブラリは *node_modules\msgpack5\dist\msgpack5.js* にあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fd424-308">要素を使用する場合 `<script>` 、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fd424-309">*msgpack5.js* する前に *signalr-protocol-msgpack.js* が参照されている場合は、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="fd424-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fd424-310">*signalr-protocol-msgpack.js* する前に *signalr.js* も必要です。</span><span class="sxs-lookup"><span data-stu-id="fd424-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fd424-311">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`にを追加する `HubConnectionBuilder` と、サーバーに接続するときに messagepack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fd424-312">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="fd424-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fd424-313">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="fd424-313">MessagePack quirks</span></span>

<span data-ttu-id="fd424-314">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="fd424-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fd424-315">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="fd424-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fd424-316">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fd424-317">たとえば、次の C# クラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="fd424-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fd424-318">JavaScript クライアントから送信する場合は、 `PascalCased` 大文字と小文字が C# クラスと正確に一致する必要があるため、プロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd424-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fd424-319">例:</span><span class="sxs-lookup"><span data-stu-id="fd424-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fd424-320">名前を使用しても `camelCased` 、C# クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fd424-321">この問題を回避するには、属性を使用して、 `Key` MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd424-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fd424-322">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fd424-323">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="fd424-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fd424-324">MessagePack プロトコルで `Kind` は、の値をエンコードする方法が提供されていません `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fd424-325">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fd424-326">現地時刻で値を使用している場合は `DateTime` 、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd424-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fd424-327">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="fd424-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fd424-328">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fd424-329">MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="fd424-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fd424-330">JavaScript クライアントによって使用される [msgpack5](https://github.com/mcollina/msgpack5) ライブラリは、 SignalR messagepack の型をサポートしていません `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="fd424-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fd424-331">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="fd424-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fd424-332">の値は `DateTime.MinValue` 、 `January 1, 0001` 値でエンコードする必要があり `timestamp96` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fd424-333">このため、 `DateTime.MinValue` JavaScript クライアントへの送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="fd424-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fd424-334">`DateTime.MinValue`が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fd424-335">通常、 `DateTime.MinValue` は、"missing" または "値" をエンコードするために使用され `null` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fd424-336">MessagePack でその値をエンコードする必要がある場合は、null 許容 `DateTime` 値 () を使用する `DateTime?` か、 `bool` 日付が存在するかどうかを示す別の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="fd424-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fd424-337">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fd424-338">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="fd424-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fd424-339">.NET クライアントとサーバーで使用される [Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="fd424-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fd424-340">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="fd424-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fd424-341">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fd424-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fd424-342">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fd424-343">シリアライザーを事前に生成したら、に渡された構成デリゲートを使用してそれらを登録でき `AddMessagePackProtocol` ます。</span><span class="sxs-lookup"><span data-stu-id="fd424-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fd424-344">MessagePack での型チェックの方が厳しい場合</span><span class="sxs-lookup"><span data-stu-id="fd424-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fd424-345">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="fd424-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fd424-346">たとえば、受信したオブジェクトのプロパティ値が number () であって `{ foo: 42 }` も、.net クラスのプロパティの型がである場合、 `string` 値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="fd424-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fd424-347">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd424-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fd424-348">この制限の詳細については、「GitHub issue [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd424-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fd424-349">関連リソース</span><span class="sxs-lookup"><span data-stu-id="fd424-349">Related resources</span></span>

* [<span data-ttu-id="fd424-350">開始するには</span><span class="sxs-lookup"><span data-stu-id="fd424-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fd424-351">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fd424-352">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="fd424-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
