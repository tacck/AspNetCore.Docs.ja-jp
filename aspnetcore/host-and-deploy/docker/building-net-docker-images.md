---
title: ASP.NET Core 向けの Docker イメージ
author: rick-anderson
description: 公開されている .NET Core Docker イメージを Docker レジストリから使用する方法について説明します。 イメージをプルして独自のイメージをビルドします。
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2b77b4fbb960a8d2dbcb67c3f4f32ba9d9459d78
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408085"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="9c811-104">ASP.NET Core 向けの Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="9c811-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="9c811-105">このチュートリアルでは、Docker コンテナー内で ASP.NET Core アプリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9c811-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="9c811-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="9c811-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="9c811-107">Microsoft .NET Core Docker イメージについて学習する</span><span class="sxs-lookup"><span data-stu-id="9c811-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="9c811-108">ASP.NET Core サンプル アプリをダウンロードする</span><span class="sxs-lookup"><span data-stu-id="9c811-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="9c811-109">サンプル アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="9c811-109">Run the sample app locally</span></span>
> * <span data-ttu-id="9c811-110">Linux コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9c811-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="9c811-111">Windows コンテナー内でサンプル アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9c811-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="9c811-112">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="9c811-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="9c811-113">ASP.NET Core の Docker イメージ</span><span class="sxs-lookup"><span data-stu-id="9c811-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="9c811-114">このチュートリアルでは、ASP.NET Core サンプル アプリをダウンロードして、Docker コンテナー内で実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="9c811-115">このサンプルは Linux コンテナーと Windows コンテナーのどちらでも動作します。</span><span class="sxs-lookup"><span data-stu-id="9c811-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="9c811-116">さまざまなコンテナー内でビルドして実行するために、サンプルの Dockerfile では [Docker のマルチステージ ビルド機能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)を使用しています。</span><span class="sxs-lookup"><span data-stu-id="9c811-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="9c811-117">ビルドと実行のコンテナーは、マイクロソフトが Docker Hub に提供しているイメージから作成されます。</span><span class="sxs-lookup"><span data-stu-id="9c811-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="9c811-118">サンプルでは、アプリをビルドするためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="9c811-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="9c811-119">イメージには、コマンド ライン ツール (CLI) が組み込まれた .NET Core SDK が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c811-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="9c811-120">イメージはローカル開発、デバッグ、および単体テスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9c811-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="9c811-121">開発とコンパイルのためにツールがインストールされているため、これは比較的大きなイメージになっています。</span><span class="sxs-lookup"><span data-stu-id="9c811-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="9c811-122">サンプルでは、アプリを実行するためにこのイメージを使用します。</span><span class="sxs-lookup"><span data-stu-id="9c811-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="9c811-123">イメージには ASP.NET Core ランタイムとライブラリが含まれており、実稼働環境でアプリを実行するために最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9c811-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="9c811-124">デプロイとアプリ起動の速度に対応した設計になっており、Docker レジストリから Docker ホストへのネットワーク パフォーマンスが最適化されていることから、イメージは比較的小さいです。</span><span class="sxs-lookup"><span data-stu-id="9c811-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="9c811-125">アプリの実行に必要なバイナリとコンテンツのみが、コンテナーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="9c811-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="9c811-126">コンテンツは実行できる状態になっており、`Docker run` からアプリの起動までを最速で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9c811-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="9c811-127">動的コード コンパイルは Docker モデルで必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9c811-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9c811-128">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9c811-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="9c811-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="9c811-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="9c811-130">.NET Core SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="9c811-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="9c811-131">Docker クライアント 18.03 以降</span><span class="sxs-lookup"><span data-stu-id="9c811-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="9c811-132">Linux ディストリビューション</span><span class="sxs-lookup"><span data-stu-id="9c811-132">Linux distributions</span></span>
    * [<span data-ttu-id="9c811-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="9c811-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="9c811-134">Debian</span><span class="sxs-lookup"><span data-stu-id="9c811-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="9c811-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="9c811-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="9c811-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="9c811-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="9c811-137">macOS</span><span class="sxs-lookup"><span data-stu-id="9c811-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="9c811-138">Windows</span><span class="sxs-lookup"><span data-stu-id="9c811-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="9c811-139">Git</span><span class="sxs-lookup"><span data-stu-id="9c811-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="9c811-140">サンプル アプリ をダウンロードする</span><span class="sxs-lookup"><span data-stu-id="9c811-140">Download the sample app</span></span>

* <span data-ttu-id="9c811-141">[.NET Core の Docker リポジトリ](https://github.com/dotnet/dotnet-docker)を複製して、サンプルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9c811-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="9c811-142">アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="9c811-142">Run the app locally</span></span>

* <span data-ttu-id="9c811-143">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9c811-144">次のコマンドを実行し、アプリをビルドしてローカルで実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="9c811-145">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="9c811-146">コマンド プロンプト上で Ctrl +C キーを押して、アプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="9c811-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="9c811-147">Linux コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="9c811-147">Run in a Linux container</span></span>

* <span data-ttu-id="9c811-148">Docker クライアント上で、Linux コンテナーに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="9c811-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="9c811-149">*dotnet-docker/samples/aspnetapp* にある Dockerfile フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="9c811-150">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="9c811-151">`build` コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9c811-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="9c811-152">イメージに aspnetapp という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9c811-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="9c811-153">現在のフォルダー内にある Dockerfile を探します (末尾にピリオド)。</span><span class="sxs-lookup"><span data-stu-id="9c811-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="9c811-154">実行コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9c811-154">The run command arguments:</span></span>
  * <span data-ttu-id="9c811-155">擬似端末を割り当てて、接続されていない場合でも開いた状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="9c811-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="9c811-156">(`--interactive --tty` と効果は同じです。)</span><span class="sxs-lookup"><span data-stu-id="9c811-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="9c811-157">コンテナーが存在する場合は、自動的に削除します。</span><span class="sxs-lookup"><span data-stu-id="9c811-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="9c811-158">ローカル コンピューター上のポート 5000 をコンテナー内のポート 80 にマップします。</span><span class="sxs-lookup"><span data-stu-id="9c811-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="9c811-159">コンテナーに aspnetcore_sample という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="9c811-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="9c811-160">aspnetapp イメージを指定します。</span><span class="sxs-lookup"><span data-stu-id="9c811-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="9c811-161">アプリをテストするには、ブラウザーで `http://localhost:5000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="9c811-162">Windows コンテナーでの実行</span><span class="sxs-lookup"><span data-stu-id="9c811-162">Run in a Windows container</span></span>

* <span data-ttu-id="9c811-163">Docker クライアント上で、Windows コンテナーに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="9c811-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="9c811-164">`dotnet-docker/samples/aspnetapp` にある Docker ファイルのフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="9c811-165">次のコマンドを実行して、Docker 内でサンプルをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="9c811-166">Windows コンテナーの場合、コンテナーの IP アドレスが必要です (`http://localhost:5000` の参照は機能しません)。</span><span class="sxs-lookup"><span data-stu-id="9c811-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="9c811-167">別のコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="9c811-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="9c811-168">`docker ps` を実行して、実行中のコンテナーを表示します。</span><span class="sxs-lookup"><span data-stu-id="9c811-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="9c811-169">"aspnetcore_sample" コンテナーがそこにあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9c811-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="9c811-170">`docker exec aspnetcore_sample ipconfig` を実行して、コンテナーの IP アドレスを表示します。</span><span class="sxs-lookup"><span data-stu-id="9c811-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="9c811-171">コマンドからの出力は、この例のようになります。</span><span class="sxs-lookup"><span data-stu-id="9c811-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="9c811-172">コンテナーの IPv4 アドレス (たとえば、172.29.245.43) をコピーして、ブラウザーのアドレス バーに貼り付けてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="9c811-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="9c811-173">手動でビルドしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="9c811-173">Build and deploy manually</span></span>

<span data-ttu-id="9c811-174">一部のシナリオでは、実行時に必要なアプリケーション ファイルにコピーすることで、アプリをコンテナーにデプロイすることを考える場合があります。</span><span class="sxs-lookup"><span data-stu-id="9c811-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="9c811-175">このセクションでは、手動によるデプロイの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9c811-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="9c811-176">*dotnet-docker/samples/aspnetapp/aspnetapp* にあるプロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9c811-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="9c811-177">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="9c811-178">コマンドの引数:</span><span class="sxs-lookup"><span data-stu-id="9c811-178">The command arguments:</span></span>
  * <span data-ttu-id="9c811-179">リリース モードでアプリケーションをビルドします (既定はデバッグ モードです)。</span><span class="sxs-lookup"><span data-stu-id="9c811-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="9c811-180">*published* フォルダーにファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9c811-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="9c811-181">アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="9c811-181">Run the application.</span></span>

  * <span data-ttu-id="9c811-182">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="9c811-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="9c811-183">Linux の場合:</span><span class="sxs-lookup"><span data-stu-id="9c811-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="9c811-184">`http://localhost:5000` を参照してホーム ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="9c811-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="9c811-185">Docker コンテナー内で手動で発行されたアプリケーションを使用するには、新しい Dockerfile を作成し、`docker build .` コマンドを使用してコンテナーをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9c811-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9c811-186">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="9c811-186">The Dockerfile</span></span>

<span data-ttu-id="9c811-187">ここに示すのは、先ほど実行した `docker build` コマンドで使用された *Dockerfile* です。</span><span class="sxs-lookup"><span data-stu-id="9c811-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9c811-188">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="9c811-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="9c811-189">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="9c811-189">The Dockerfile</span></span>

<span data-ttu-id="9c811-190">ここに示すのは、先ほど実行した `docker build` コマンドで使用された *Dockerfile* です。</span><span class="sxs-lookup"><span data-stu-id="9c811-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="9c811-191">このセクションで実行したときと同じ方法で `dotnet publish` を使用して、ビルドとデプロイを行います。</span><span class="sxs-lookup"><span data-stu-id="9c811-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="9c811-192">前の Dockerfile で示されているように、`*.csproj` ファイルは別個の "*レイヤー*" としてコピーおよび復元されます。</span><span class="sxs-lookup"><span data-stu-id="9c811-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="9c811-193">`docker build` コマンドを使用してイメージをビルドすると、組み込みのキャッシュが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9c811-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="9c811-194">`docker build` コマンドが最後に実行されてから `*.csproj` ファイルが変更されていない場合、`dotnet restore` コマンドを再度実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9c811-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="9c811-195">代わりに、対応する `dotnet restore` レイヤーの組み込みキャッシュが再利用されます。</span><span class="sxs-lookup"><span data-stu-id="9c811-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="9c811-196">詳細については、「[Dockerfile を記述するためのベスト プラクティス](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c811-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9c811-197">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9c811-197">Additional resources</span></span>

* [<span data-ttu-id="9c811-198">Docker の build コマンド</span><span class="sxs-lookup"><span data-stu-id="9c811-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="9c811-199">Docker の run コマンド</span><span class="sxs-lookup"><span data-stu-id="9c811-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="9c811-200">[ASP.NET Core の Docker サンプル](https://github.com/dotnet/dotnet-docker) (このチュートリアルで使用されたものです。)</span><span class="sxs-lookup"><span data-stu-id="9c811-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="9c811-201">プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する</span><span class="sxs-lookup"><span data-stu-id="9c811-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="9c811-202">Visual Studio Docker ツールの使用</span><span class="sxs-lookup"><span data-stu-id="9c811-202">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="9c811-203">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="9c811-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="9c811-204">ドッカーと小さなコンテナを使用した GC</span><span class="sxs-lookup"><span data-stu-id="9c811-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="9c811-205">次の手順</span><span class="sxs-lookup"><span data-stu-id="9c811-205">Next steps</span></span>

<span data-ttu-id="9c811-206">同じアプリを格納している Git リポジトリにも、ドキュメントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9c811-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="9c811-207">リポジトリ内にある利用可能なリソースの概要については、[README ファイル](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9c811-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="9c811-208">特に、HTTPS を実装する方法について確認してください。</span><span class="sxs-lookup"><span data-stu-id="9c811-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="9c811-209">「[Developing ASP.NET Core Applications with Docker over HTTPS (Docker を使用して HTTPS による ASP.NET Core アプリケーションを開発する)](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)」</span><span class="sxs-lookup"><span data-stu-id="9c811-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>
