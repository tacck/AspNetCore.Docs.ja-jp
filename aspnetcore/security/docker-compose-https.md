---
title: HTTPS を使用した docker 構成を使用したコンテナー内の ASP.NET Core イメージのホスト
author: ravipal
description: HTTPS 経由で Docker Compose を使用して ASP.NET Core イメージをホストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-compose-https
ms.openlocfilehash: a44e82be9c631aae788a671b514bab3b70f54522
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407802"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="453fa-103">HTTPS 経由の Docker Compose を使用した ASP.NET Core イメージのホスト</span><span class="sxs-lookup"><span data-stu-id="453fa-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="453fa-104">[既定では、](/aspnet/core/security/enforcing-ssl)ASP.NET Core で HTTPS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="453fa-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="453fa-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS)は、信頼、id、および暗号化のための[証明書](https://en.wikipedia.org/wiki/Public_key_certificate)に依存します。</span><span class="sxs-lookup"><span data-stu-id="453fa-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="453fa-106">このドキュメントでは、HTTPS で事前に構築されたコンテナーイメージを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="453fa-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="453fa-107">開発シナリオについては、「 [HTTPS 経由の Docker を使用した ASP.NET Core アプリケーションの開発](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="453fa-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="453fa-108">このサンプルでは、docker [17.06](https://docs.docker.com/release-notes/docker-ce)以降の[docker クライアント](https://www.docker.com/products/docker)が必要です。</span><span class="sxs-lookup"><span data-stu-id="453fa-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="453fa-109">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="453fa-109">Prerequisites</span></span>

<span data-ttu-id="453fa-110">このドキュメントの一部の手順では、 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download)以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="453fa-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="453fa-111">証明書</span><span class="sxs-lookup"><span data-stu-id="453fa-111">Certificates</span></span>

<span data-ttu-id="453fa-112">ドメインの[運用ホスト](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)には、[証明機関](https://wikipedia.org/wiki/Certificate_authority)からの証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="453fa-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="453fa-113">[Let's Encrypt](https://letsencrypt.org/)は、無料の証明書を提供する証明機関です。</span><span class="sxs-lookup"><span data-stu-id="453fa-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="453fa-114">このドキュメントでは、事前に構築されたイメージをホストするために[自己署名の開発証明書](https://wikipedia.org/wiki/Self-signed_certificate)を使用 `localhost` します。</span><span class="sxs-lookup"><span data-stu-id="453fa-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="453fa-115">手順は、実稼働証明書の使用に似ています。</span><span class="sxs-lookup"><span data-stu-id="453fa-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="453fa-116">実稼働証明書の場合:</span><span class="sxs-lookup"><span data-stu-id="453fa-116">For production certificates:</span></span>

* <span data-ttu-id="453fa-117">`dotnet dev-certs`ツールは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="453fa-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="453fa-118">手順で使用した場所に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="453fa-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="453fa-119">サイトディレクトリの外部にある任意の場所に証明書を格納します。</span><span class="sxs-lookup"><span data-stu-id="453fa-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="453fa-120">次のセクションに記載されている手順では、 `volumes` *docker-compose.yml*のプロパティを使用して、証明書をコンテナーにマウントします。</span><span class="sxs-lookup"><span data-stu-id="453fa-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="453fa-121">Dockerfile でコマンドを使用してコンテナーイメージに証明書を追加することもでき `COPY` ますが、この方法はお勧めしません。 *Dockerfile*</span><span class="sxs-lookup"><span data-stu-id="453fa-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="453fa-122">証明書をイメージにコピーすることは、次の理由から推奨されません。</span><span class="sxs-lookup"><span data-stu-id="453fa-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="453fa-123">これにより、開発者証明書でのテストに同じイメージを使用することが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="453fa-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="453fa-124">これにより、運用証明書でのホストに同じイメージを使用することが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="453fa-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="453fa-125">証明書の公開には大きなリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="453fa-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="453fa-126">Docker 構成を使用して https をサポートするコンテナーを開始する</span><span class="sxs-lookup"><span data-stu-id="453fa-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="453fa-127">オペレーティングシステムの構成については、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="453fa-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="453fa-128">Linux コンテナーを使用した Windows</span><span class="sxs-lookup"><span data-stu-id="453fa-128">Windows using Linux containers</span></span>

<span data-ttu-id="453fa-129">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="453fa-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="453fa-130">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="453fa-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="453fa-131">次の内容を含む_docker-compose.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="453fa-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="453fa-132">Docker 構成ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="453fa-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="453fa-133">HTTPS 用に構成された ASP.NET Core でコンテナーを起動します。</span><span class="sxs-lookup"><span data-stu-id="453fa-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="453fa-134">macOS または Linux</span><span class="sxs-lookup"><span data-stu-id="453fa-134">macOS or Linux</span></span>

<span data-ttu-id="453fa-135">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="453fa-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="453fa-136">`dotnet dev-certs https --trust`は、macOS と Windows でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="453fa-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="453fa-137">ディストリビューションでサポートされている方法で Linux 上の証明書を信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="453fa-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="453fa-138">ブラウザーで証明書を信頼する必要があると考えられます。</span><span class="sxs-lookup"><span data-stu-id="453fa-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="453fa-139">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="453fa-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="453fa-140">次の内容を含む_docker-compose.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="453fa-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="453fa-141">Docker 構成ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="453fa-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="453fa-142">HTTPS 用に構成された ASP.NET Core でコンテナーを起動します。</span><span class="sxs-lookup"><span data-stu-id="453fa-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="453fa-143">Windows コンテナーを使用した windows</span><span class="sxs-lookup"><span data-stu-id="453fa-143">Windows using Windows containers</span></span>

<span data-ttu-id="453fa-144">証明書を生成してローカルコンピューターを構成する:</span><span class="sxs-lookup"><span data-stu-id="453fa-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="453fa-145">上記のコマンドで、を `{ password here }` パスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="453fa-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="453fa-146">次の内容を含む_docker-compose.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="453fa-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="453fa-147">Docker 構成ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="453fa-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="453fa-148">HTTPS 用に構成された ASP.NET Core でコンテナーを起動します。</span><span class="sxs-lookup"><span data-stu-id="453fa-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
