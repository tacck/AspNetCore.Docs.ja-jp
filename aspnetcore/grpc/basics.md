---
<span data-ttu-id="fb564-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb564-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb564-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb564-102">'Blazor'</span></span>
- <span data-ttu-id="fb564-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb564-103">'Identity'</span></span>
- <span data-ttu-id="fb564-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb564-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb564-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb564-105">'Razor'</span></span>
- <span data-ttu-id="fb564-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="fb564-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-services-with-c"></a><span data-ttu-id="fb564-107">C\# を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="fb564-107">gRPC services with C\#</span></span>

<span data-ttu-id="fb564-108">このドキュメントでは、C# で [gRPC](https://grpc.io/docs/guides/) アプリを作成するために必要な概念を説明します。</span><span class="sxs-lookup"><span data-stu-id="fb564-108">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="fb564-109">ここで取り上げるトピックは、[C-core](https://grpc.io/blog/grpc-stacks)ベースと ASP.NET Core ベースの両方の gRPC アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-109">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="fb564-110">proto ファイル</span><span class="sxs-lookup"><span data-stu-id="fb564-110">proto file</span></span>

<span data-ttu-id="fb564-111">gRPC では、API 開発に対してコントラクト優先のアプローチが使われます。</span><span class="sxs-lookup"><span data-stu-id="fb564-111">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="fb564-112">プロトコル バッファー (protobuf) は、既定でインターフェイス デザイン言語 (IDL) として使用されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-112">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="fb564-113">*\*.proto* ファイルには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="fb564-113">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="fb564-114">gRPC サービスの定義。</span><span class="sxs-lookup"><span data-stu-id="fb564-114">The definition of the gRPC service.</span></span>
* <span data-ttu-id="fb564-115">クライアントとサーバー間で送信されるメッセージ。</span><span class="sxs-lookup"><span data-stu-id="fb564-115">The messages sent between clients and servers.</span></span>

<span data-ttu-id="fb564-116">Protobuf ファイルの構文の詳細については、[公式ドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fb564-116">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="fb564-117">たとえば、[gRPC サービスの概要に関するページ](xref:tutorials/grpc/grpc-start)で使用されている *greet.proto* ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="fb564-117">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="fb564-118">`Greeter` サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="fb564-118">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="fb564-119">`Greeter` サービスで `SayHello` 呼び出しを定義します。</span><span class="sxs-lookup"><span data-stu-id="fb564-119">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="fb564-120">`SayHello` では、`HelloRequest` メッセージを送信し、`HelloReply` メッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="fb564-120">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="fb564-121">C\# アプリに .proto ファイルを追加する</span><span class="sxs-lookup"><span data-stu-id="fb564-121">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="fb564-122">*\*.proto* ファイルは、`<Protobuf>` 項目グループにそれを追加することで、プロジェクトに含まれます。</span><span class="sxs-lookup"><span data-stu-id="fb564-122">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="fb564-123">.proto ファイルに対する C# ツール サポート</span><span class="sxs-lookup"><span data-stu-id="fb564-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="fb564-124">*\*.proto* ファイルから C# アセットを生成するために、ツール パッケージ [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) が必要です。</span><span class="sxs-lookup"><span data-stu-id="fb564-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="fb564-125">生成されるアセット (ファイル) は:</span><span class="sxs-lookup"><span data-stu-id="fb564-125">The generated assets (files):</span></span>

* <span data-ttu-id="fb564-126">プロジェクトがビルドされるたびに、必要に応じて生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="fb564-127">プロジェクトに追加されないか、ソース管理にチェックインされません。</span><span class="sxs-lookup"><span data-stu-id="fb564-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="fb564-128">*obj* ディレクトリに格納されるビルド成果物です。</span><span class="sxs-lookup"><span data-stu-id="fb564-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="fb564-129">このパッケージは、サーバー プロジェクトとクライアント プロジェクトの両方で必要です。</span><span class="sxs-lookup"><span data-stu-id="fb564-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="fb564-130">`Grpc.AspNetCore` メタパッケージには、`Grpc.Tools` への参照が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fb564-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="fb564-131">サーバー プロジェクトでは、Visual Studio の Package Manager を使用するか、プロジェクト ファイルに `<PackageReference>` を追加することによって、`Grpc.AspNetCore` を追加できます。</span><span class="sxs-lookup"><span data-stu-id="fb564-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="fb564-132">クライアント プロジェクトでは、gRPC クライアントを使用するために必要なその他のパッケージと共に、`Grpc.Tools` を直接参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fb564-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="fb564-133">ツール パッケージは実行時に不要であるため、依存関係に `PrivateAssets="All"` でマークが付けられます。</span><span class="sxs-lookup"><span data-stu-id="fb564-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="fb564-134">生成された C# アセット</span><span class="sxs-lookup"><span data-stu-id="fb564-134">Generated C# assets</span></span>

<span data-ttu-id="fb564-135">ツール パッケージは、含まれている *\*.proto* ファイルに定義されたメッセージを表す C# 型を生成します。</span><span class="sxs-lookup"><span data-stu-id="fb564-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="fb564-136">サーバー側アセットの場合、抽象サービスの基本型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="fb564-137">基本型には、 *.proto* ファイルに含まれるすべての gRPC 呼び出しの定義が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fb564-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="fb564-138">この基本型から派生し、gRPC 呼び出しのロジックを実装する具象サービス実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="fb564-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="fb564-139">`greet.proto` の場合、前述の例では、仮想 `SayHello` メソッドを含む抽象 `GreeterBase` 型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="fb564-140">具象実装 `GreeterService` は、メソッドをオーバーライドし、gRPC 呼び出しを処理するロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="fb564-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="fb564-141">クライアント側アセットの場合、具象クライアント型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="fb564-142">*.proto* ファイル内の gRPC 呼び出しは、具象型のメソッドに変換され、これを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="fb564-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="fb564-143">`greet.proto` の場合、前述の例では、具象 `GreeterClient` 型が生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="fb564-144">`GreeterClient.SayHelloAsync` を呼び出して、サーバーへの gRPC 呼び出しを開始します。</span><span class="sxs-lookup"><span data-stu-id="fb564-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="fb564-145">既定で、`<Protobuf>` 項目グループに含まれている各 *\*.proto* ファイルに対して、サーバーとクライアントのアセットが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fb564-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="fb564-146">サーバー プロジェクトでサーバー アセットのみが生成されるようにするには、`GrpcServices` 属性を `Server` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fb564-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="fb564-147">同様に、クライアントプロジェクトでは属性を `Client` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fb564-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb564-148">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fb564-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
