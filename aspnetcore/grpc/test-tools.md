---
title: ASP.NET Core で gRPCurl を使用して gRPC サービスをテストする
author: jamesnk
description: gRPC ツールを使用してサービスをテストする方法について説明します。 gRPCurl は、gRPC サービスと対話するためのコマンドライン ツールです。 gRPCui は、対話型の Web UI です。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 800b320413552e73f05e0359e67eeb2caf4e0e2a
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770169"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="02807-105">ASP.NET Core で gRPCurl を使用して gRPC サービスをテストする</span><span class="sxs-lookup"><span data-stu-id="02807-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="02807-106">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="02807-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="02807-107">gRPC 用のツールが用意されており、開発者は、クライアント アプリをビルドせずにサービスをテストできます。</span><span class="sxs-lookup"><span data-stu-id="02807-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="02807-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) は、gRPC サービスとの対話を提供するコマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="02807-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="02807-109">[gRPCui](https://github.com/fullstorydev/grpcui) は gRPCurl の上にビルドされ、Postman や Swagger UI などのツールと同様に、gRPC 用の対話型の Web UI が追加されています。</span><span class="sxs-lookup"><span data-stu-id="02807-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="02807-110">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="02807-110">This article discusses how to:</span></span>

* <span data-ttu-id="02807-111">gRPCurl と gRPCui をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="02807-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="02807-112">gRPC ASP.NET Core アプリを使用して、gRPC リフレクションを設定します。</span><span class="sxs-lookup"><span data-stu-id="02807-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="02807-113">`grpcurl` を使用して、gRPC サービスの検出とテストを行います。</span><span class="sxs-lookup"><span data-stu-id="02807-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="02807-114">`grpcui` を使用して、ブラウザー経由で gRPC サービスと対話します。</span><span class="sxs-lookup"><span data-stu-id="02807-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="02807-115">gRPCurl について</span><span class="sxs-lookup"><span data-stu-id="02807-115">About gRPCurl</span></span>

<span data-ttu-id="02807-116">gRPCurl は、gRPC コミュニティによって作成されたコマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="02807-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="02807-117">次のような機能があります。</span><span class="sxs-lookup"><span data-stu-id="02807-117">Its features include:</span></span>

* <span data-ttu-id="02807-118">ストリーミング サービスを含む、gRPC サービスの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="02807-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="02807-119">[gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)を使用したサービスの検出。</span><span class="sxs-lookup"><span data-stu-id="02807-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="02807-120">gRPC サービスの一覧表示と説明。</span><span class="sxs-lookup"><span data-stu-id="02807-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="02807-121">セキュリティで保護されたサーバー (TLS) と安全ではない (プレーンテキスト) サーバーの使用。</span><span class="sxs-lookup"><span data-stu-id="02807-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="02807-122">`grpcurl` のダウンロードとインストールの詳細については、[gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl#installation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02807-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![gRPCurl コマンド ライン](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="02807-124">gRPC リフレクションの設定</span><span class="sxs-lookup"><span data-stu-id="02807-124">Set up gRPC reflection</span></span>

<span data-ttu-id="02807-125">`grpcurl` により、サービスを呼び出す前に、サービスの Protobuf コントラクトが認識されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="02807-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="02807-126">この作業を実行する 2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="02807-126">There are two ways to do this:</span></span>

* <span data-ttu-id="02807-127">サーバーで [gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) を設定します。</span><span class="sxs-lookup"><span data-stu-id="02807-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="02807-128">gRPCurl により、サービス コントラクトが自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="02807-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="02807-129">gRPCurl のコマンドライン引数に `.proto` ファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="02807-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="02807-130">gRPC リフレクションを設定して gRPCurl を使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="02807-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="02807-131">gRPC リフレクションにより、クライアントがサービスを検出するために呼び出すことができるアプリに新しい gRPC サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="02807-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="02807-132">gRPC ASP.NET Core には、[`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) パッケージとの gRPC リフレクションのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="02807-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="02807-133">アプリでリフレクションを構成するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="02807-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="02807-134">`Grpc.AspNetCore.Server.Reflection` パッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="02807-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="02807-135">`Startup.cs` にリフレクションを登録します。</span><span class="sxs-lookup"><span data-stu-id="02807-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="02807-136">`AddGrpcReflection` でリフレクションを有効にするサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="02807-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="02807-137">`MapGrpcReflectionService` を使用して、リフレクション サービス エンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="02807-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="02807-138">gRPC リフレクションが設定されると、以下が行われます。</span><span class="sxs-lookup"><span data-stu-id="02807-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="02807-139">gRPC リフレクション サービスがサーバー アプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="02807-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="02807-140">gRPC リフレクションをサポートするクライアント アプリで、リフレクション サービスを呼び出して、サーバーによってホストされるサービスを検出できます。</span><span class="sxs-lookup"><span data-stu-id="02807-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="02807-141">gRPC サービスは引き続きクライアントから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="02807-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="02807-142">リフレクションにより、サービス検出のみが有効にされ、サーバー側のセキュリティはバイパスされません。</span><span class="sxs-lookup"><span data-stu-id="02807-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="02807-143">[認証と承認](xref:grpc/authn-and-authz)によって保護されるエンドポイントにより、呼び出し側は、エンドポイントが正常に呼び出されるための資格情報を渡す必要があります。</span><span class="sxs-lookup"><span data-stu-id="02807-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="02807-144">`grpcurl` を使用します</span><span class="sxs-lookup"><span data-stu-id="02807-144">Use `grpcurl`</span></span>

<span data-ttu-id="02807-145">`-help` 引数では、`grpcurl` コマンドライン オプションについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="02807-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="02807-146">サービスの検出</span><span class="sxs-lookup"><span data-stu-id="02807-146">Discover services</span></span>

<span data-ttu-id="02807-147">サーバーによって定義されたサービスを表示するには、`describe` 動詞を使用します。</span><span class="sxs-lookup"><span data-stu-id="02807-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="02807-148">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="02807-148">The preceding example:</span></span>

* <span data-ttu-id="02807-149">サーバー `localhost:5001` で `describe` Verb を実行します。</span><span class="sxs-lookup"><span data-stu-id="02807-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="02807-150">gRPC リフレクションによって返されたサービスとメソッドを出力します。</span><span class="sxs-lookup"><span data-stu-id="02807-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="02807-151">`Greeter` は、アプリによって実装されるサービスです。</span><span class="sxs-lookup"><span data-stu-id="02807-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="02807-152">`ServerReflection` は、`Grpc.AspNetCore.Server.Reflection` パッケージによって追加されたサービスです。</span><span class="sxs-lookup"><span data-stu-id="02807-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="02807-153">詳細を表示するには、`describe` をサービス、メソッド、またはメッセージ名と組み合わせます。</span><span class="sxs-lookup"><span data-stu-id="02807-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="02807-154">gRPC サービスの呼び出し</span><span class="sxs-lookup"><span data-stu-id="02807-154">Call gRPC services</span></span>

<span data-ttu-id="02807-155">要求メッセージを表す JSON 引数と共にサービス名とメソッド名を指定して、gRPC サービスを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="02807-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="02807-156">JSON は Protobuf に変換され、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="02807-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="02807-157">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="02807-157">In the preceding example:</span></span>

* <span data-ttu-id="02807-158">`-d` 引数を使用して、JSON を含む要求メッセージを指定します。</span><span class="sxs-lookup"><span data-stu-id="02807-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="02807-159">この引数は、サーバー アドレスとメソッド名の前で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="02807-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="02807-160">`greeter.Greeter` サービスの `SayHello` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="02807-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="02807-161">応答メッセージを JSON として出力します。</span><span class="sxs-lookup"><span data-stu-id="02807-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="02807-162">gRPCui について</span><span class="sxs-lookup"><span data-stu-id="02807-162">About gRPCui</span></span>

<span data-ttu-id="02807-163">gRPCui は、gRPC の対話型 Web UI です。</span><span class="sxs-lookup"><span data-stu-id="02807-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="02807-164">gRPCurl の上にビルドされ、Postman や Swagger UI などの HTTP ツールと同様に、gRPC サービスを検出してテストするための GUI が提供されます。</span><span class="sxs-lookup"><span data-stu-id="02807-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="02807-165">`grpcui` のダウンロードとインストールの詳細については、[gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui#installation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02807-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="02807-166">`grpcui` の使用</span><span class="sxs-lookup"><span data-stu-id="02807-166">Using `grpcui`</span></span>

<span data-ttu-id="02807-167">対話するサーバー アドレスを引数に指定して `grpcui` を実行します。</span><span class="sxs-lookup"><span data-stu-id="02807-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="02807-168">このツールによってブラウザー ウィンドウが起動され、対話型の Web UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="02807-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="02807-169">gRPC サービスは、gRPC リフレクションを使用して、自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="02807-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="02807-171">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="02807-171">Additional resources</span></span>

* [<span data-ttu-id="02807-172">gRPCurl GitHub ホームページ</span><span class="sxs-lookup"><span data-stu-id="02807-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="02807-173">gRPCui GitHub ホームページ</span><span class="sxs-lookup"><span data-stu-id="02807-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
