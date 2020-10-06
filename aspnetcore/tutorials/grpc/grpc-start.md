---
title: ASP.NET Core で .NET Core gRPC のクライアントとサーバーを作成する
author: juntaoluo
description: このチュートリアルでは、ASP.NET Core で gRPC サービスと gRPC クライアントを作成する方法を示します。 gRPC サービス プロジェクトの作成方法、proto ファイルの編集方法、二重ストリーミング呼び出しの追加方法について学習します。
ms.author: johluo
ms.date: 04/08/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424257"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="ea84c-104">チュートリアル: ASP.NET Core で gRPC のクライアントとサーバーを作成する</span><span class="sxs-lookup"><span data-stu-id="ea84c-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="ea84c-105">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ea84c-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ea84c-106">このチュートリアルでは、.NET Core [gRPC](https://grpc.io/docs/guides/) クライアントと ASP.NET Core gRPC サーバーを作成する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="ea84c-107">最終的に、gRPC あいさつサービスと通信する gRPC クライアントが与えられます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="ea84c-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ea84c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ea84c-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="ea84c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ea84c-110">gRPC サービスを作成する。</span><span class="sxs-lookup"><span data-stu-id="ea84c-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="ea84c-111">gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="ea84c-112">gRPC あいさつサービスで gRPC クライアント サービスをテストする。</span><span class="sxs-lookup"><span data-stu-id="ea84c-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ea84c-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ea84c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea84c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="ea84c-117">gRPC サービスの作成</span><span class="sxs-lookup"><span data-stu-id="ea84c-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea84c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea84c-119">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="ea84c-120">または、Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ea84c-121">**[新しいプロジェクトの作成]** ダイアログで、 **[gRPC サービス]** を選択して、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![[新しいプロジェクトの作成] ダイアログ](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="ea84c-123">プロジェクトに **GrpcGreeter** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ea84c-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *GrpcGreeter* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="ea84c-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="ea84c-125">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-125">Select **Create**.</span></span>
* <span data-ttu-id="ea84c-126">**[Create a new gRPC service]\(新しい gPRC サービスの作成\)** ダイアログで、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ea84c-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="ea84c-127">**gRPC サービス** テンプレートが選択されています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="ea84c-128">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea84c-130">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ea84c-131">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ea84c-132">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ea84c-133">`dotnet new` コマンドでは、*GrpcGreeter* フォルダー内に新しい gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ea84c-134">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *GrpcGreeter* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ea84c-135">"**ビルドとデバッグに必要な資産が 'GrpcGreeter' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="ea84c-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ea84c-136">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea84c-138">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="ea84c-139">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、gRPC サービスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ea84c-140">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="ea84c-140">Open the project</span></span>

<span data-ttu-id="ea84c-141">Visual Studio から、 **[ファイル]**  >  **[開く]** の順に選択し、*GrpcGreeter.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="ea84c-142">サービスを実行する</span><span class="sxs-lookup"><span data-stu-id="ea84c-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="ea84c-143">サービスが `https://localhost:5001` でリッスンしていることがログに示されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="ea84c-144">gRPC テンプレートは[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="ea84c-145">gRPC クライアントでは、HTTPS を使用してサーバーを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="ea84c-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="ea84c-146">macOS の場合、ASP.NET Core gRPC と TLS の組み合わせに対応していません。</span><span class="sxs-lookup"><span data-stu-id="ea84c-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ea84c-147">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="ea84c-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ea84c-148">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="ea84c-149">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="ea84c-149">Examine the project files</span></span>

<span data-ttu-id="ea84c-150">*GrpcGreeter*プロジェクト ファイル:</span><span class="sxs-lookup"><span data-stu-id="ea84c-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="ea84c-151">*greet.proto*:*Protos/greet.proto* ファイルは、`Greeter` gRPC を定義し、gRPC サーバー資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ea84c-152">詳細については、「[gRPC の概要](xref:grpc/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="ea84c-153">*Services* フォルダー:`Greeter` サービスの実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ea84c-154">*appSettings.json*:Kestrel で使用されるプロトコルなどの構成データが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ea84c-155">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ea84c-156">*Program.cs*:gRPC サービスのエントリ ポイントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ea84c-157">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="ea84c-158">*Startup.cs*:アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="ea84c-159">詳細については、[アプリの Startup](xref:fundamentals/startup)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="ea84c-160">.NET コンソール アプリで gRPC クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="ea84c-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea84c-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea84c-162">Visual Studio のインスタンスをもう 1 つ開き、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="ea84c-163">**[新しいプロジェクトの作成]** ダイアログで、 **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="ea84c-164">**[プロジェクト名]** テキスト ボックスに「**GrpcGreeterClient**」を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-164">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea84c-166">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ea84c-167">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ea84c-168">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea84c-170">「[Visual Studio for Mac を使用した macOS での完全な .NET Core ソリューションの構築](/dotnet/core/tutorials/using-on-mac-vs-full-solution)」の手順に従って、*GrpcGreeterClient* という名前のコンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="ea84c-171">必要なパッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="ea84c-171">Add required packages</span></span>

<span data-ttu-id="ea84c-172">gRPC クライアント プロジェクトには、次のパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="ea84c-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="ea84c-173">.NET Core のクライアントを含む [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)。</span><span class="sxs-lookup"><span data-stu-id="ea84c-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="ea84c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)。これに C# の protobuf メッセージ API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="ea84c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)。これには protobuf ファイルの C# ツール サポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="ea84c-176">ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea84c-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea84c-178">パッケージ マネージャー コンソール (PMC) または NuGet パッケージの管理を使用してパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ea84c-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="ea84c-179">パッケージをインストールするための PMC オプション</span><span class="sxs-lookup"><span data-stu-id="ea84c-179">PMC option to install packages</span></span>

* <span data-ttu-id="ea84c-180">Visual Studio で **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="ea84c-181">**[パッケージ マネージャー コンソール]** ウィンドウから `cd GrpcGreeterClient` を実行し、*GrpcGreeterClient.csproj* ファイルが含まれるフォルダーにディレクトリを変更します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="ea84c-182">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="ea84c-183">パッケージをインストールするための [NuGet パッケージの管理] オプション</span><span class="sxs-lookup"><span data-stu-id="ea84c-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="ea84c-184">**[ソリューション エクスプローラー]**  >  **[NuGet パッケージの管理]** でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="ea84c-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="ea84c-185">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="ea84c-186">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ea84c-187">**[参照]** タブから **Grpc.Net.Client** パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="ea84c-188">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ea84c-190">**統合ターミナル**から次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-191">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ea84c-192">**[Solution Pad]**  >  **[パッケージを追加]** で **[パッケージ]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="ea84c-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="ea84c-193">検索ボックスに「**Grpc.Net.Client**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ea84c-194">結果ウィンドウから **Grpc.Net.Client** パッケージを選択し、 **[パッケージを追加]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ea84c-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="ea84c-195">`Google.Protobuf` と `Grpc.Tools` に同じ手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="ea84c-196">greet.proto を追加する</span><span class="sxs-lookup"><span data-stu-id="ea84c-196">Add greet.proto</span></span>

* <span data-ttu-id="ea84c-197">gRPC クライアント プロジェクトで *Protos* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="ea84c-198">gRPC あいさつサービスから gRPC クライアント プロジェクトに *Protos\greet.proto* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="ea84c-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="ea84c-199">`greet.proto` ファイル内の名前空間を、プロジェクトの名前空間に更新します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-199">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="ea84c-200">*GrpcGreeterClient.csproj* プロジェクト ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-200">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="ea84c-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-201">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="ea84c-202">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-202">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="ea84c-204">*GrpcGreeterClient.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-204">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="ea84c-206">プロジェクトを右クリックし、 **[ツール]**  >  **[ファイルの編集]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-206">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="ea84c-207">*greet.proto* ファイルを参照する `<Protobuf>` 要素で項目グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-207">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="ea84c-208">Greeter クライアントを作成する</span><span class="sxs-lookup"><span data-stu-id="ea84c-208">Create the Greeter client</span></span>

<span data-ttu-id="ea84c-209">プロジェクトをビルドして、`GrpcGreeter` 名前空間内に型を作成します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-209">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="ea84c-210">`GrpcGreeter` 型は、ビルド プロセスによって自動的に生成されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-210">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="ea84c-211">次のコードを使用して、gRPC クライアントの *Program.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-211">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="ea84c-212">*Program.cs* には、gRPC クライアントのエントリ ポイントとロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ea84c-212">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="ea84c-213">Greeter クライアントは、次の方法で作成されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-213">The Greeter client is created by:</span></span>

* <span data-ttu-id="ea84c-214">gRPC サービスへの接続を作成するための情報が含まれている `GrpcChannel` をインスタンス化する。</span><span class="sxs-lookup"><span data-stu-id="ea84c-214">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="ea84c-215">`GrpcChannel` を使用して、Greeter クライアントを構築します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-215">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="ea84c-216">Greeter クライアントから非同期の `SayHello` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-216">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="ea84c-217">`SayHello` 呼び出しの結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-217">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="ea84c-218">gRPC あいさつサービスで gRPC クライアントをテストする</span><span class="sxs-lookup"><span data-stu-id="ea84c-218">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea84c-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea84c-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea84c-220">あいさつサービスで、`Ctrl+F5` キーを押して、デバッガーなしでサーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-220">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="ea84c-221">`GrpcGreeterClient` プロジェクトで、`Ctrl+F5` 押してデバッガーなしでクライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-221">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea84c-222">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea84c-222">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea84c-223">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-223">Start the Greeter service.</span></span>
* <span data-ttu-id="ea84c-224">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-224">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea84c-225">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ea84c-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ea84c-226">あいさつサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-226">Start the Greeter service.</span></span>
* <span data-ttu-id="ea84c-227">クライアントを起動します。</span><span class="sxs-lookup"><span data-stu-id="ea84c-227">Start the client.</span></span>

---

<span data-ttu-id="ea84c-228">クライアントにより、その名前 *GreeterClient* が含まれるあいさつメッセージが、サービスに送信されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-228">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="ea84c-229">サービスから応答として "Hello GreeterClient" のメッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-229">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="ea84c-230">"Hello GreeterClient" の応答がコマンド プロンプトに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-230">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="ea84c-231">gRPC サービスにより、成功した呼び出しの詳細が、コマンド プロンプトに書き込まれるログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="ea84c-231">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="ea84c-232">この記事のコードでは、gRPC サービスをセキュリティで保護するために、ASP.NET Core HTTPS 開発証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="ea84c-232">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="ea84c-233">.NET gRPC クライアントが `The remote certificate is invalid according to the validation procedure.` または `The SSL connection could not be established.` というメッセージで失敗する場合、その開発証明書は信頼されていません。</span><span class="sxs-lookup"><span data-stu-id="ea84c-233">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="ea84c-234">この問題を解決するには、「[信頼されていないか無効な証明書で gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea84c-234">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="ea84c-235">次の手順</span><span class="sxs-lookup"><span data-stu-id="ea84c-235">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
