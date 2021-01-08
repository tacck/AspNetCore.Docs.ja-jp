---
title: .NET を使用したコードファーストの gRPC サービスとクライアント
author: jamesnk
description: .NET を使用したコードファーストの gRPC を作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880619"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="99a3d-103">.NET を使用したコードファーストの gRPC サービスとクライアント</span><span class="sxs-lookup"><span data-stu-id="99a3d-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="99a3d-104">作成者: [James Newton-King](https://twitter.com/jamesnk)、[Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="99a3d-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="99a3d-105">コードファースト gRPC では、.NET の型を使用して、サービスとメッセージ コントラクトを定義します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="99a3d-106">コードファーストは、システム全体で .NET を使用する場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="99a3d-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="99a3d-107">.Net サービスとデータ コントラクトの型は、.NET サーバーとクライアントの間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="99a3d-108">`.proto` ファイルおよびコード生成でコントラクトを定義する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="99a3d-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="99a3d-109">複数の言語の多言語システムでは、コードファーストはお勧めできません。</span><span class="sxs-lookup"><span data-stu-id="99a3d-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="99a3d-110">.NET サービスとデータ コントラクトの型は、.NET 以外のプラットフォームでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="99a3d-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="99a3d-111">コードファーストを使用して作成された gRPC サービスを呼び出すには、他のプラットフォームでサービスと一致する `.proto` コントラクトを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99a3d-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="99a3d-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="99a3d-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="99a3d-113">protobuf-net.Grpc の支援が必要な場合は、[protobuf-net.Grpc の Web サイト](https://protobuf-net.github.io/protobuf-net.Grpc/)を参照するか、または [protobuf-net.Grpc GitHub リポジトリ](https://github.com/protobuf-net/protobuf-net.Grpc)でイシューを作成してください。</span><span class="sxs-lookup"><span data-stu-id="99a3d-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="99a3d-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) はコミュニティ プロジェクトであり、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="99a3d-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="99a3d-115">これにより、`Grpc.AspNetCore` と `Grpc.Net.Client` に対するコードファーストのサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="99a3d-116">属性で注釈が付けられた .NET 型を使用して、アプリの gRPC サービスとメッセージが定義されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="99a3d-117">コードファーストの gRPC サービスを作成するための最初の手順は、コード コントラクトを定義することです。</span><span class="sxs-lookup"><span data-stu-id="99a3d-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="99a3d-118">サーバーとクライアントによって共有される新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="99a3d-119">[protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) パッケージ リファレンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="99a3d-120">サービスとデータ コントラクトの型を作成します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="99a3d-121">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-121">The preceding code:</span></span>

* <span data-ttu-id="99a3d-122">`HelloRequest` および `HelloReply` メッセージを定義します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="99a3d-123">単項 `SayHelloAsync` gRPC メソッドを使用して `IGreeterService` コントラクト インターフェイスを定義します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="99a3d-124">サービス コントラクトがサーバーに実装され、クライアントから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="99a3d-125">サービス インターフェイスに定義されているメソッドは、単項、サーバー ストリーミング、クライアント ストリーミング、または双方向ストリーミングのいずれであるかによって、特定の署名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99a3d-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="99a3d-126">サービス コントラクトの定義の詳細については、[protobuf-net.Grpc の概要ドキュメント](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99a3d-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="99a3d-127">コードファースト gRPC サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="99a3d-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="99a3d-128">ASP.NET Core アプリに gRPC コードファースト サービスを追加するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="99a3d-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="99a3d-129">[protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) パッケージ リファレンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="99a3d-130">共有コードコントラクト プロジェクトへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="99a3d-131">新しい `GreeterService.cs` ファイルを作成し、`IGreeterService` サービス インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="99a3d-132">`Startup.cs` ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="99a3d-133">上記のコードにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-133">In the preceding code:</span></span>

* <span data-ttu-id="99a3d-134">`AddCodeFirstGrpc` により、コードファーストを有効にするサービスが登録されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="99a3d-135">`MapGrpcService<GreeterService>` により、コードファーストのサービス エンドポイントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="99a3d-136">コードファーストで実装された gRPC サービスと `.proto` ファイルは、同じアプリ内で共存できます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="99a3d-137">すべての gRPC サービスでは、[gRPC サービス構成](xref:grpc/configuration#configure-services-options)が使されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="99a3d-138">コードファースト gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="99a3d-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="99a3d-139">コードファースト gRPC クライアントは、サービス コントラクトを使用して gRPC サービスを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="99a3d-140">コードファースト クライアントを使用して gRPC サービスを呼び出すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="99a3d-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="99a3d-141">[protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) パッケージ リファレンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="99a3d-142">共有コードコントラクト プロジェクトへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="99a3d-143">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-143">The preceding code:</span></span>

* <span data-ttu-id="99a3d-144">gRPC チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="99a3d-145">`CreateGrpcService<IGreeterService>` 拡張メソッドを使用して、チャネルからコードファースト クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="99a3d-146">`SayHelloAsync` を使用して gRPC サービスを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="99a3d-147">コードファースト gRPC クライアントがチャネルから作成されます。</span><span class="sxs-lookup"><span data-stu-id="99a3d-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="99a3d-148">通常のクライアントと同様に、コードファースト クライアントは、その[チャネル構成](xref:grpc/configuration#configure-client-options)を使用します。</span><span class="sxs-lookup"><span data-stu-id="99a3d-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99a3d-149">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="99a3d-149">Additional resources</span></span>

* [<span data-ttu-id="99a3d-150">protobuf-net.Grpc Web サイト</span><span class="sxs-lookup"><span data-stu-id="99a3d-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="99a3d-151">protobuf-net.Grpc GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="99a3d-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
