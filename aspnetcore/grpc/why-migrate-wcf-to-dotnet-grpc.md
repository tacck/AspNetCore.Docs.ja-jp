---
title: ASP.NET Core gRPC に WCF を移行する理由
author: markrendle
description: この記事では、最新のアーキテクチャおよびプラットフォームへの移行を望んでいる Windows Communication Foundation (WCF) 開発者に ASP.NET Core gRPC が適している理由の概要を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058689"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="e1833-103">Windows Communication Foundation (WCF) 開発者向けの gRPC</span><span class="sxs-lookup"><span data-stu-id="e1833-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="e1833-104">この記事では、最新のアーキテクチャおよびプラットフォームへの移行を望んでいる Windows Communication Foundation (WCF) 開発者に ASP.NET Core gRPC が適している理由の概要を説明します。</span><span class="sxs-lookup"><span data-stu-id="e1833-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="e1833-105">WCF との比較</span><span class="sxs-lookup"><span data-stu-id="e1833-105">Comparison to WCF</span></span>

<span data-ttu-id="e1833-106">gRPC における実装およびアプローチは異なりますが、gRPC でのサービスの開発および利用のエクスペリエンスは、WCF 開発者にとって直観的なものとなります。</span><span class="sxs-lookup"><span data-stu-id="e1833-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="e1833-107">WCF と gRPC は、次に示す同じ目標を掲げた RPC (リモート プロシージャ コール) フレームワークです:</span><span class="sxs-lookup"><span data-stu-id="e1833-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="e1833-108">クライアントとサーバーが同じプラットフォーム上にあるかのようにコードを書くことができるようにすること。</span><span class="sxs-lookup"><span data-stu-id="e1833-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="e1833-109">ポータブル ネットワーク API の簡素化を実現すること。</span><span class="sxs-lookup"><span data-stu-id="e1833-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="e1833-110">この 2 つのプラットフォームはインターフェイスを宣言するプロセスはそれぞれ異なりますが、インターフェイスの宣言および実装の要件を共有します。</span><span class="sxs-lookup"><span data-stu-id="e1833-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="e1833-111">gRPC がサポートする多くの種類の RPC 呼び出しは、WCF サービスで使用可能なバインディングに適切にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e1833-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="e1833-112">詳細と例については、「[WCF ソリューションを gRPC に移行する](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1833-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="e1833-113">gRPC の利点</span><span class="sxs-lookup"><span data-stu-id="e1833-113">Benefits of gRPC</span></span>

<span data-ttu-id="e1833-114">次の理由により、gRPC は他のアプローチよりも優れたフレームワークを提供します。</span><span class="sxs-lookup"><span data-stu-id="e1833-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="e1833-115">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="e1833-115">Performance</span></span>

<span data-ttu-id="e1833-116">gRPC では HTTP/2 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e1833-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="e1833-117">HTTP/1.1 とは対照的に、HTTP/2 は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e1833-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="e1833-118">より小規模で高速なバイナリ プロトコルです。</span><span class="sxs-lookup"><span data-stu-id="e1833-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="e1833-119">コンピューターが解析を行う場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="e1833-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="e1833-120">単一の接続での多重化要求がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e1833-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="e1833-121">多重化すると、複数の要求を単一の接続で送信することができ、要求が相互にブロックし合うことはありません。</span><span class="sxs-lookup"><span data-stu-id="e1833-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="e1833-122">HTTP/1.1 では、ブロックは "ヘッドオブライン (HOL) ブロック" と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="e1833-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="e1833-123">gRPC によって、効率的なバイナリ形式である Protobuf が使用され、メッセージがシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="e1833-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="e1833-124">Protobuf メッセージは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e1833-124">Protobuf messages are:</span></span>
* <span data-ttu-id="e1833-125">シリアル化および逆シリアル化が迅速に行われます。</span><span class="sxs-lookup"><span data-stu-id="e1833-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="e1833-126">使用する帯域幅がテキストベースの形式よりも小さくなります。</span><span class="sxs-lookup"><span data-stu-id="e1833-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="e1833-127">gRPC は、帯域幅に制限があるモバイルデバイスおよびネットワークに適したソリューションです。</span><span class="sxs-lookup"><span data-stu-id="e1833-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="e1833-128">相互運用性</span><span class="sxs-lookup"><span data-stu-id="e1833-128">Interoperability</span></span>

<span data-ttu-id="e1833-129">主要なすべてのプログラミング言語とプラットフォーム (.NET、Java、Python、Go、C++、Node.js、Swift、Dart、Ruby、PHP など) 用に gRPC ツールおよびライブラリが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e1833-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="e1833-130">Protobuf バイナリ ワイヤ形式と、各プラットフォームでの効率的なコード生成により、開発者はクロスプラットフォームのパフォーマンスに優れたアプリを構築できます。</span><span class="sxs-lookup"><span data-stu-id="e1833-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="e1833-131">使いやすさと生産性</span><span class="sxs-lookup"><span data-stu-id="e1833-131">Usability and productivity</span></span>

<span data-ttu-id="e1833-132">gRPC は包括的な RPC ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="e1833-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="e1833-133">複数の言語とプラットフォームで一貫して動作します。</span><span class="sxs-lookup"><span data-stu-id="e1833-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="e1833-134">また、優れたツールも用意されていて、定型コードの大部分が自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e1833-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="e1833-135">WCF と同様に、gRPC でもメッセージと厳密に型指定されたクライアントが自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e1833-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="e1833-136">開発者の時間は解放され、ビジネスロジックに集中できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e1833-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="e1833-137">ストリーム</span><span class="sxs-lookup"><span data-stu-id="e1833-137">Streaming</span></span>

<span data-ttu-id="e1833-138">gRPC には、WCF の全二重サービスと同様の機能を提供する、完全な双方向ストリーミングが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e1833-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="e1833-139">gRPC ストリーミングは、通常のインターネット接続、ロード バランサー、およびサービス メッシュを介して動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="e1833-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="e1833-140">期限、タイムアウト、およびキャンセル</span><span class="sxs-lookup"><span data-stu-id="e1833-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="e1833-141">gRPC を使用すると、RPC が終了するまでの最長時間をクライアントで指定できます。</span><span class="sxs-lookup"><span data-stu-id="e1833-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="e1833-142">指定した期限を超過した場合、サーバーではクライアントに関係なく操作をキャンセルすることができます。</span><span class="sxs-lookup"><span data-stu-id="e1833-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="e1833-143">期限とキャンセルを後続の gRPC 呼び出しを通じて伝達すると、リソース使用量の制限を楽に適用することができます。</span><span class="sxs-lookup"><span data-stu-id="e1833-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="e1833-144">クライアントでは、期限を超過したとき、または必要に応じてそれよりも早く操作を停止することができます。</span><span class="sxs-lookup"><span data-stu-id="e1833-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="e1833-145">たとえば、クライアントによって、ユーザーの介入を理由に操作を停止することができます。</span><span class="sxs-lookup"><span data-stu-id="e1833-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="e1833-146">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="e1833-146">Security</span></span>

<span data-ttu-id="e1833-147">gRPC で TLS と HTTP/2 を使用して、クライアントとサーバーの間にエンドツーエンドの暗号化された接続を提供できます。</span><span class="sxs-lookup"><span data-stu-id="e1833-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="e1833-148">クライアント証明書の認証がサポートされているので、クライアントとサーバーの間のセキュリティと信頼性が向上します。</span><span class="sxs-lookup"><span data-stu-id="e1833-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="e1833-149">WCF から .NET Core および .NET 5 への移行パスとしての gRPC</span><span class="sxs-lookup"><span data-stu-id="e1833-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="e1833-150">.NET Core と .NET 5 は、さまざまなプラットフォームにわたってサービスを提供したい開発者に Microsoft がリモート通信ソリューションを提供する方法に変化をもたらします。</span><span class="sxs-lookup"><span data-stu-id="e1833-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="e1833-151">.NET Core と .NET 5 により、[WCF サービスの呼び出し](/dotnet/core/additional-tools/wcf-web-service-reference-guide)はサポートされますが、WCF をホストするためのサーバー側のサポートは提供されません。</span><span class="sxs-lookup"><span data-stu-id="e1833-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="e1833-152">WCF アプリを最新化するためのパスとして推奨されるのは、次の 2 つです。</span><span class="sxs-lookup"><span data-stu-id="e1833-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="e1833-153">gRPC は最新のテクノロジに基づいて構築されていて、RPC アプリの開発者コミュニティで最も人気のある選択肢として登場しています。</span><span class="sxs-lookup"><span data-stu-id="e1833-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="e1833-154">.NET Core 3.0 以降、最新の .NET プラットフォームによって gRPC に対する優れたサポートが提供されています。</span><span class="sxs-lookup"><span data-stu-id="e1833-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="e1833-155">gRPC を使用するように WCF サービスを移行すると、最新のアプリで必要とされる RPC 機能、パフォーマンス、および相互運用性を容易に実現できます。</span><span class="sxs-lookup"><span data-stu-id="e1833-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="e1833-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) とは、WCF サービスをホストするためのサポートを .NET Core および .NET 5 に導入するためのコミュニティの取り組みです。</span><span class="sxs-lookup"><span data-stu-id="e1833-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="e1833-157">プレビュー リリースが利用できます。プロジェクトは実稼働に対応するための準備に取り組んでいます。</span><span class="sxs-lookup"><span data-stu-id="e1833-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="e1833-158">CoreWCF でサポートされるのは、WCF の機能のサブセットのみとなっているため、それを使用できるように移行する .NET Framework アプリによって、コードの変更とテストが正常に行われる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1833-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="e1833-159">WCF サービスを呼び出す既存のクライアントとの互換性がアプリで維持される必要がある場合、CoreWCF は適切な選択肢となります。</span><span class="sxs-lookup"><span data-stu-id="e1833-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="e1833-160">作業開始</span><span class="sxs-lookup"><span data-stu-id="e1833-160">Get started</span></span>

<span data-ttu-id="e1833-161">ASP.NET Core で gRPC サービスを構築する場合の WCF 開発者向けの詳細なガイダンスについては、「[WCF 開発者向け ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1833-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
