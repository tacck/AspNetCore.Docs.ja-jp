---
title: gRPC ツールを使用したサービスのテスト
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594374"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="c0278-105">gRPC ツールを使用したサービスのテスト</span><span class="sxs-lookup"><span data-stu-id="c0278-105">Test services with gRPC tools</span></span>

<span data-ttu-id="c0278-106">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c0278-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c0278-107">ツールは gRPC で使用できます。これにより、開発者は、クライアント アプリをビルドせずにサービスをテストできます。</span><span class="sxs-lookup"><span data-stu-id="c0278-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="c0278-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) は、gRPC サービスとの対話を提供するコマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="c0278-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="c0278-109">[gRPCui](https://github.com/fullstorydev/grpcui) では、gRPC 用の対話型 Web UI が追加されます。</span><span class="sxs-lookup"><span data-stu-id="c0278-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="c0278-110">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c0278-110">This article discusses how to:</span></span>

* <span data-ttu-id="c0278-111">gRPCurl と gRPCui をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="c0278-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="c0278-112">gRPC ASP.NET Core を使用して、gRPC リフレクションをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="c0278-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="c0278-113">`grpcurl` を使用して、gRPC サービスの検出とテストを行います。</span><span class="sxs-lookup"><span data-stu-id="c0278-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="c0278-114">`grpcui` を使用して、ブラウザー経由で gRPC サービスと対話します。</span><span class="sxs-lookup"><span data-stu-id="c0278-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="c0278-115">gRPCurl について</span><span class="sxs-lookup"><span data-stu-id="c0278-115">About gRPCurl</span></span>

<span data-ttu-id="c0278-116">gRPCurl は、gRPC コミュニティによって作成されたコマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="c0278-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="c0278-117">次のような機能があります。</span><span class="sxs-lookup"><span data-stu-id="c0278-117">Its features include:</span></span>

* <span data-ttu-id="c0278-118">ストリーミング サービスを含む、gRPC サービスの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="c0278-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="c0278-119">[gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)を使用したサービスの検出。</span><span class="sxs-lookup"><span data-stu-id="c0278-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="c0278-120">gRPC サービスの一覧表示と説明。</span><span class="sxs-lookup"><span data-stu-id="c0278-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="c0278-121">セキュリティで保護されたサーバー (TLS) と安全ではない (プレーンテキスト) サーバーの使用。</span><span class="sxs-lookup"><span data-stu-id="c0278-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="c0278-122">`grpcurl` のダウンロードとインストールの詳細については、[gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl#installation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0278-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="c0278-123">gRPC リフレクションの設定</span><span class="sxs-lookup"><span data-stu-id="c0278-123">Setup gRPC reflection</span></span>

<span data-ttu-id="c0278-124">`grpcurl` では、サービスを呼び出す前に、サービスの Protobuf コントラクトの認識が行われている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0278-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="c0278-125">この作業を実行する 2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="c0278-125">There are two ways to do this:</span></span>

* <span data-ttu-id="c0278-126">gRPC リフレクションを使用して、サービス コントラクトを検出します。</span><span class="sxs-lookup"><span data-stu-id="c0278-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="c0278-127">コマンドライン引数に *.proto* ファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="c0278-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="c0278-128">gRPC リフレクションとサービス検出で gRPCurl を使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="c0278-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="c0278-129">gRPC ASP.NET Core には、[Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) パッケージを使用した gRPC リフレクションのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="c0278-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="c0278-130">アプリでリフレクションを構成するには、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0278-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="c0278-131">`Grpc.AspNetCore.Server.Reflection` パッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="c0278-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="c0278-132">*Startup.cs* にリフレクションを登録します。</span><span class="sxs-lookup"><span data-stu-id="c0278-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="c0278-133">`AddGrpcReflection` でリフレクションを有効にするサービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c0278-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="c0278-134">`MapGrpcReflectionService` でリフレクション サービス エンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="c0278-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="c0278-135">`grpcurl` を使用します</span><span class="sxs-lookup"><span data-stu-id="c0278-135">Use `grpcurl`</span></span>

<span data-ttu-id="c0278-136">`-help` 引数では、`grpcurl` コマンドライン オプションについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="c0278-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="c0278-137">サービスの検出</span><span class="sxs-lookup"><span data-stu-id="c0278-137">Discover services</span></span>

<span data-ttu-id="c0278-138">サーバーによって定義されたサービスを表示するには、`describe` 動詞を使用します。</span><span class="sxs-lookup"><span data-stu-id="c0278-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="c0278-139">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="c0278-139">The preceding example:</span></span>

* <span data-ttu-id="c0278-140">サーバー `localhost:5001` で動詞 `describe` を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0278-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="c0278-141">gRPC リフレクションによって返されたサービスとメソッドを出力します。</span><span class="sxs-lookup"><span data-stu-id="c0278-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="c0278-142">`Greeter` は、アプリによって実装されるサービスです。</span><span class="sxs-lookup"><span data-stu-id="c0278-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="c0278-143">`ServerReflection` は、`Grpc.AspNetCore.Server.Reflection` パッケージによって追加されたサービスです。</span><span class="sxs-lookup"><span data-stu-id="c0278-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="c0278-144">`describe` と、サービス名、メソッド名、またはメッセージ名を組み合わせて、詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="c0278-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="c0278-145">gRPC サービスの呼び出し</span><span class="sxs-lookup"><span data-stu-id="c0278-145">Call gRPC services</span></span>

<span data-ttu-id="c0278-146">要求メッセージを表す JSON 引数と共にサービス名とメソッド名を指定して、gRPC サービスを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c0278-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="c0278-147">JSON は Protobuf に変換され、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="c0278-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="c0278-148">上記の例の場合:</span><span class="sxs-lookup"><span data-stu-id="c0278-148">The preceding example:</span></span>

* <span data-ttu-id="c0278-149">`-d` 引数によって、JSON を含む要求メッセージが指定されます。</span><span class="sxs-lookup"><span data-stu-id="c0278-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="c0278-150">この引数は、サーバー アドレスとメソッド名の前で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0278-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="c0278-151">`greeter.Greeter` サービスの `SayHello` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c0278-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="c0278-152">応答メッセージを JSON として出力します。</span><span class="sxs-lookup"><span data-stu-id="c0278-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="c0278-153">gRPCui について</span><span class="sxs-lookup"><span data-stu-id="c0278-153">About gRPCui</span></span>

<span data-ttu-id="c0278-154">gRPCui は、gRPC の対話型 Web UI です。</span><span class="sxs-lookup"><span data-stu-id="c0278-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="c0278-155">これは、gRPCurl の上にビルドされ、Postman などの HTTP ツールと同様に、gRPC サービスを検出してテストするための GUI を提供します。</span><span class="sxs-lookup"><span data-stu-id="c0278-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="c0278-156">`grpcui` のダウンロードとインストールの詳細については、[gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui#installation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0278-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="c0278-157">`grpcui` の使用</span><span class="sxs-lookup"><span data-stu-id="c0278-157">Using `grpcui`</span></span>

<span data-ttu-id="c0278-158">対話するサーバー アドレスを使用して、`grpcui` を引数として実行します。</span><span class="sxs-lookup"><span data-stu-id="c0278-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="c0278-159">このツールを使用すると、ブラウザー ウィンドウが開き、対話型 Web UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c0278-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="c0278-160">gRPC サービスは、gRPC リフレクションを使用して、自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="c0278-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="c0278-162">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c0278-162">Additional resources</span></span>

* [<span data-ttu-id="c0278-163">gRPCurl GitHub ホームページ</span><span class="sxs-lookup"><span data-stu-id="c0278-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="c0278-164">gRPCui GitHub ホームページ</span><span class="sxs-lookup"><span data-stu-id="c0278-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="c0278-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="c0278-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
