---
<span data-ttu-id="d406d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-102">'Blazor'</span></span>
- <span data-ttu-id="d406d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-103">'Identity'</span></span>
- <span data-ttu-id="d406d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-105">'Razor'</span></span>
- <span data-ttu-id="d406d-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="d406d-107">.NET 構成のための gRPC</span><span class="sxs-lookup"><span data-stu-id="d406d-107">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="d406d-108">サービス オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="d406d-108">Configure services options</span></span>

<span data-ttu-id="d406d-109">gRPC サービスは、*Startup.cs* の `AddGrpc` によって構成されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-109">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="d406d-110">次の表では、gRPC サービスを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d406d-110">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="d406d-111">オプション</span><span class="sxs-lookup"><span data-stu-id="d406d-111">Option</span></span> | <span data-ttu-id="d406d-112">既定値</span><span class="sxs-lookup"><span data-stu-id="d406d-112">Default Value</span></span> | <span data-ttu-id="d406d-113">説明</span><span class="sxs-lookup"><span data-stu-id="d406d-113">Description</span></span> |
| ---
<span data-ttu-id="d406d-114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-115">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-115">'Blazor'</span></span>
- <span data-ttu-id="d406d-116">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-116">'Identity'</span></span>
- <span data-ttu-id="d406d-117">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-117">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-118">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-118">'Razor'</span></span>
- <span data-ttu-id="d406d-119">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-119">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-120">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-120">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-121">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-121">'Blazor'</span></span>
- <span data-ttu-id="d406d-122">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-122">'Identity'</span></span>
- <span data-ttu-id="d406d-123">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-123">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-124">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-124">'Razor'</span></span>
- <span data-ttu-id="d406d-125">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-125">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-127">'Blazor'</span></span>
- <span data-ttu-id="d406d-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-128">'Identity'</span></span>
- <span data-ttu-id="d406d-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-130">'Razor'</span></span>
- <span data-ttu-id="d406d-131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-133">'Blazor'</span></span>
- <span data-ttu-id="d406d-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-134">'Identity'</span></span>
- <span data-ttu-id="d406d-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-136">'Razor'</span></span>
- <span data-ttu-id="d406d-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-139">'Blazor'</span></span>
- <span data-ttu-id="d406d-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-140">'Identity'</span></span>
- <span data-ttu-id="d406d-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-142">'Razor'</span></span>
- <span data-ttu-id="d406d-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-143">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-144">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-144">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-145">'Blazor'</span></span>
- <span data-ttu-id="d406d-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-146">'Identity'</span></span>
- <span data-ttu-id="d406d-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-148">'Razor'</span></span>
- <span data-ttu-id="d406d-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-151">'Blazor'</span></span>
- <span data-ttu-id="d406d-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-152">'Identity'</span></span>
- <span data-ttu-id="d406d-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-154">'Razor'</span></span>
- <span data-ttu-id="d406d-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-157">'Blazor'</span></span>
- <span data-ttu-id="d406d-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-158">'Identity'</span></span>
- <span data-ttu-id="d406d-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-160">'Razor'</span></span>
- <span data-ttu-id="d406d-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-161">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-162">------ | | MaxSendMessageSize | `null` |サーバーから送信できる最大メッセージ サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="d406d-162">------ | | MaxSendMessageSize | `null` | The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="d406d-163">構成された最大メッセージ サイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="d406d-163">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> <span data-ttu-id="d406d-164">`null` に設定する場合、メッセージ サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d406d-164">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="d406d-165">| | MaxReceiveMessageSize | 4 MB | サーバーで受信できる最大メッセージ サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="d406d-165">| | MaxReceiveMessageSize | 4 MB | The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="d406d-166">サーバーでこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d406d-166">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="d406d-167">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d406d-167">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> <span data-ttu-id="d406d-168">`null` に設定する場合、メッセージ サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d406d-168">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="d406d-169">| | EnableDetailedErrors | `false` | `true` の場合、サービス メソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-169">| | EnableDetailedErrors | `false` | If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="d406d-170">既定値は、`false` です。</span><span class="sxs-lookup"><span data-stu-id="d406d-170">The default is `false`.</span></span> <span data-ttu-id="d406d-171">`EnableDetailedErrors` を `true` に設定すると、機密情報が漏洩する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d406d-171">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> <span data-ttu-id="d406d-172">| | CompressionProviders | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="d406d-172">| | CompressionProviders | gzip | A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="d406d-173">カスタム圧縮プロバイダーを作成し、コレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="d406d-173">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="d406d-174">既定で構成されているプロバイダーは、**gzip** 圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d406d-174">The default configured providers support **gzip** compression.</span></span> <span data-ttu-id="d406d-175">| | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | サーバーから送信されるメッセージの圧縮に使用される圧縮アルゴリズム。</span><span class="sxs-lookup"><span data-stu-id="d406d-175">| | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="d406d-176">このアルゴリズムは、`CompressionProviders` の圧縮プロバイダーと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="d406d-176">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="d406d-177">アルゴリズムで応答を圧縮するには、そのアルゴリズムをサポートしていることをクライアントが **grpc-accept-encoding** ヘッダーで送信することによって、そのことを示す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d406d-177">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> <span data-ttu-id="d406d-178">| | ResponseCompressionLevel | `null` | サーバーから送信されるメッセージの圧縮に使用される圧縮レベル。</span><span class="sxs-lookup"><span data-stu-id="d406d-178">| | ResponseCompressionLevel | `null` | The compress level used to compress messages sent from the server.</span></span> <span data-ttu-id="d406d-179">| | Interceptors | None | 各 gRPC 呼び出しで実行されるインターセプターのコレクション。</span><span class="sxs-lookup"><span data-stu-id="d406d-179">| | Interceptors | None | A collection of interceptors that are run with each gRPC call.</span></span> <span data-ttu-id="d406d-180">インターセプターは、登録されている順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-180">Interceptors are run in the order they are registered.</span></span> <span data-ttu-id="d406d-181">グローバルに構成されたインターセプターは、1 つのサービスに対してインターセプターが構成される前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-181">Globally configured interceptors are run before interceptors configured for a single service.</span></span> <span data-ttu-id="d406d-182">gRPC インターセプターの詳細については、「[gRPC インターセプターとミドルウェア](xref:grpc/migration#grpc-interceptors-vs-middleware)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d406d-182">For more information about gRPC interceptors, see [gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware).</span></span> <span data-ttu-id="d406d-183">| | IgnoreUnknownServices | `false` | If `true` の場合、不明なサービスおよびメソッドへの呼び出しでは **UNIMPLEMENTED** 状態が返されず、要求は ASP.NET Core に登録された次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-183">| | IgnoreUnknownServices | `false` | If `true`, calls to unknown services and methods don't return an **UNIMPLEMENTED** status, and the request passes to the next registered middleware in ASP.NET Core.</span></span> |

<span data-ttu-id="d406d-184">`Startup.ConfigureServices` の `AddGrpc` 呼び出しに対して options デリゲートを指定することで、すべてのサービスに対してオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="d406d-184">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="d406d-185">1 つのサービスのオプションは、`AddGrpc` で提供されるグローバル オプションをオーバーライドします。また、`AddServiceOptions<TService>` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="d406d-185">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="d406d-186">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="d406d-186">Configure client options</span></span>

<span data-ttu-id="d406d-187">gRPC のクライアント構成は `GrpcChannelOptions` で設定されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-187">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="d406d-188">次の表では、gRPC チャネルを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d406d-188">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="d406d-189">オプション</span><span class="sxs-lookup"><span data-stu-id="d406d-189">Option</span></span> | <span data-ttu-id="d406d-190">既定値</span><span class="sxs-lookup"><span data-stu-id="d406d-190">Default Value</span></span> | <span data-ttu-id="d406d-191">説明</span><span class="sxs-lookup"><span data-stu-id="d406d-191">Description</span></span> |
| ---
<span data-ttu-id="d406d-192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-193">'Blazor'</span></span>
- <span data-ttu-id="d406d-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-194">'Identity'</span></span>
- <span data-ttu-id="d406d-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-196">'Razor'</span></span>
- <span data-ttu-id="d406d-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-197">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-198">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-198">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-199">'Blazor'</span></span>
- <span data-ttu-id="d406d-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-200">'Identity'</span></span>
- <span data-ttu-id="d406d-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-202">'Razor'</span></span>
- <span data-ttu-id="d406d-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-205">'Blazor'</span></span>
- <span data-ttu-id="d406d-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-206">'Identity'</span></span>
- <span data-ttu-id="d406d-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-208">'Razor'</span></span>
- <span data-ttu-id="d406d-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-211">'Blazor'</span></span>
- <span data-ttu-id="d406d-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-212">'Identity'</span></span>
- <span data-ttu-id="d406d-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-214">'Razor'</span></span>
- <span data-ttu-id="d406d-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-217">'Blazor'</span></span>
- <span data-ttu-id="d406d-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-218">'Identity'</span></span>
- <span data-ttu-id="d406d-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-220">'Razor'</span></span>
- <span data-ttu-id="d406d-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-221">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-222">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-222">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-223">'Blazor'</span></span>
- <span data-ttu-id="d406d-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-224">'Identity'</span></span>
- <span data-ttu-id="d406d-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-226">'Razor'</span></span>
- <span data-ttu-id="d406d-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-229">'Blazor'</span></span>
- <span data-ttu-id="d406d-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-230">'Identity'</span></span>
- <span data-ttu-id="d406d-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-232">'Razor'</span></span>
- <span data-ttu-id="d406d-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d406d-234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d406d-234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d406d-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d406d-235">'Blazor'</span></span>
- <span data-ttu-id="d406d-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d406d-236">'Identity'</span></span>
- <span data-ttu-id="d406d-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d406d-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="d406d-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d406d-238">'Razor'</span></span>
- <span data-ttu-id="d406d-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d406d-239">'SignalR' uid:</span></span> 

<span data-ttu-id="d406d-240">------ | | HttpHandler | 新しいインスタンス | gRPC 呼び出しを行うために使用する `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="d406d-240">------ | | HttpHandler | New instance | The `HttpMessageHandler` used to make gRPC calls.</span></span> <span data-ttu-id="d406d-241">カスタム `HttpClientHandler` を構成したり、gRPC 呼び出しの HTTP パイプラインに追加のハンドラーを加えたりするために、クライアントを設定できます。</span><span class="sxs-lookup"><span data-stu-id="d406d-241">A client can be set to configure a custom `HttpClientHandler` or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="d406d-242">`HttpMessageHandler` が指定されていない場合、自動廃棄のチャネルのために新しい `HttpClientHandler` インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-242">If no `HttpMessageHandler` is specified, a new `HttpClientHandler` instance is created for the channel with automatic disposal.</span></span> <span data-ttu-id="d406d-243">| | HttpClient | `null` | gRPC 呼び出しを行うために使用される `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="d406d-243">| | HttpClient | `null` | The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="d406d-244">この設定は、`HttpHandler` に代わる方法です。</span><span class="sxs-lookup"><span data-stu-id="d406d-244">This setting is an alternative to `HttpHandler`.</span></span> <span data-ttu-id="d406d-245">| | DisposeHttpClient | `false` | `true` に設定し、`HttpMessageHandler` または `HttpClient` が指定されている場合、`GrpcChannel` が廃棄されたときに `HttpHandler` または `HttpClient` (該当するもの) が廃棄されます。</span><span class="sxs-lookup"><span data-stu-id="d406d-245">| | DisposeHttpClient | `false` | If set to `true` and an `HttpMessageHandler` or `HttpClient` is specified, then either the `HttpHandler` or `HttpClient`, respectively, is disposed when the `GrpcChannel` is disposed.</span></span> <span data-ttu-id="d406d-246">| | LoggerFactory | `null` | gRPC 呼び出しに関する情報をログに記録するため、クライアントによって使用される `LoggerFactory`。</span><span class="sxs-lookup"><span data-stu-id="d406d-246">| | LoggerFactory | `null` | The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="d406d-247">`LoggerFactory` インスタンスは、依存関係の挿入から解決することも、`LoggerFactory.Create` を使用して作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d406d-247">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="d406d-248">ログ記録を構成する例については、「<xref:grpc/diagnostics#grpc-client-logging>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d406d-248">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> <span data-ttu-id="d406d-249">| | MaxSendMessageSize | `null` |クライアントから送信できる最大メッセージ サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="d406d-249">| | MaxSendMessageSize | `null` | The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="d406d-250">構成された最大メッセージ サイズを超えるメッセージを送信しようとすると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="d406d-250">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> <span data-ttu-id="d406d-251">`null` に設定する場合、メッセージ サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d406d-251">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="d406d-252">| | <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | クライアントで受信できる最大メッセージ サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="d406d-252">| | <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="d406d-253">クライアントでこの制限を超えるメッセージを受信すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d406d-253">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="d406d-254">この値を大きくすると、クライアントはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d406d-254">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> <span data-ttu-id="d406d-255">`null` に設定する場合、メッセージ サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="d406d-255">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="d406d-256">| | 資格情報 |`null` | `ChannelCredentials` インスタンス。</span><span class="sxs-lookup"><span data-stu-id="d406d-256">| | Credentials | `null` | A `ChannelCredentials` instance.</span></span> <span data-ttu-id="d406d-257">資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用します。</span><span class="sxs-lookup"><span data-stu-id="d406d-257">Credentials are used to add authentication metadata to gRPC calls.</span></span> <span data-ttu-id="d406d-258">| | CompressionProviders | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="d406d-258">| | CompressionProviders | gzip | A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="d406d-259">カスタム圧縮プロバイダーを作成し、コレクションに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="d406d-259">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="d406d-260">既定で構成されているプロバイダーは、**gzip** 圧縮をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d406d-260">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="d406d-261">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d406d-261">The following code:</span></span>

* <span data-ttu-id="d406d-262">チャネルで送受信する最大メッセージ サイズを設定します。</span><span class="sxs-lookup"><span data-stu-id="d406d-262">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="d406d-263">クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="d406d-263">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="d406d-264">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d406d-264">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
