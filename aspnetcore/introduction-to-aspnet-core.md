---
title: ASP.NET Core の概要
author: rick-anderson
description: インターネットに接続された最新のクラウド対応アプリを構築するための、クロス プラットフォームで高パフォーマンスのオープン ソース フレームワークである ASP.NET Core について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: f58c25ec8b47b2bd8d425d0bfd0d52df9bbc2655
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408202"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="abc40-103">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="abc40-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="abc40-104">著者: [Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="abc40-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abc40-105">ASP.NET Core は、インターネットに接続された最新のクラウド対応アプリを構築するための、クロス プラットフォームで高パフォーマンスの[オープン ソース](https://github.com/dotnet/aspnetcore) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="abc40-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="abc40-106">ASP.NET Core では次のことができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="abc40-107">Web アプリ、Web サービス、[モノのインターネット (IoT)](https://www.microsoft.com/internet-of-things/) アプリ、モバイル バックエンドを構築する。</span><span class="sxs-lookup"><span data-stu-id="abc40-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="abc40-108">Windows、macOS、Linux で好みの開発ツールを使う。</span><span class="sxs-lookup"><span data-stu-id="abc40-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="abc40-109">クラウドまたはオンプレミスに展開する。</span><span class="sxs-lookup"><span data-stu-id="abc40-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="abc40-110">[[.NET Core]](/dotnet/core/introduction) で実行する。</span><span class="sxs-lookup"><span data-stu-id="abc40-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="abc40-111">ASP.NET Core を選ぶ理由</span><span class="sxs-lookup"><span data-stu-id="abc40-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="abc40-112">何百万もの開発者は、[ASP.NET 4.x](/aspnet/overview) を使用して、Web アプリを作成しました。</span><span class="sxs-lookup"><span data-stu-id="abc40-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="abc40-113">ASP.NET Core は ASP.NET 4.x を設計し直したものであり、無駄のないモジュール形式のフレームワークになるようなアーキテクチャ変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="abc40-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="abc40-114">ASP.NET Core MVC を使って Web API と Web UI を構築する</span><span class="sxs-lookup"><span data-stu-id="abc40-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="abc40-115">ASP.NET Core MVC は、[Web API](xref:tutorials/first-web-api) と [Web アプリ](xref:tutorials/razor-pages/index)を構築する機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="abc40-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="abc40-116">[モデル ビュー コントローラー (MVC) パターン](xref:mvc/overview)は、Web API と Web アプリをテスト可能にするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="abc40-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="abc40-117">[Razor Pages](xref:razor-pages/index) はページ ベースのプログラミング モデルであり、Web UI の開発を容易にし、生産性を高めます。</span><span class="sxs-lookup"><span data-stu-id="abc40-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="abc40-118">[Razor マークアップ](xref:mvc/views/razor)では、[Razor Pages](xref:razor-pages/index) および [MVC ビュー](xref:mvc/views/overview)用に生産性の高い構文が提供されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="abc40-119">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="abc40-120">[複数のデータ形式とコンテンツ ネゴシエーション](xref:web-api/advanced/formatting)の組み込みサポートにより、Web API はブラウザーやモバイル デバイスなどのさまざまなクライアントと接続できます。</span><span class="sxs-lookup"><span data-stu-id="abc40-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="abc40-121">[モデル バインド](xref:mvc/models/model-binding)は、HTTP 要求からアクション メソッドのパラメーターにデータを自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="abc40-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="abc40-122">[モデル検証](xref:mvc/models/validation)では、クライアント側とサーバー側の検証が自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="abc40-123">クライアント側の開発</span><span class="sxs-lookup"><span data-stu-id="abc40-123">Client-side development</span></span>

<span data-ttu-id="abc40-124">ASP.NET Core は、人気のあるクライアント側のフレームワークとライブラリ ([Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react)、[Bootstrap](https://getbootstrap.com/) など) をシームレスに統合します。</span><span class="sxs-lookup"><span data-stu-id="abc40-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="abc40-125">詳細については、<xref:blazor/index> と "*クライアント側の開発*" の関連トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="abc40-126">ASP.NET Core ターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="abc40-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="abc40-127">ASP.NET Core 3.x 以降では、.NET Core のみをターゲットにできます。</span><span class="sxs-lookup"><span data-stu-id="abc40-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="abc40-128">一般に、ASP.NET Core は [.NET Standard](/dotnet/standard/net-standard) ライブラリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="abc40-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="abc40-129">.NET Standard 2.0 を使って作成されたライブラリは、[.NET Standard 2.0 を実装しているすべての .NET プラットフォーム](/dotnet/standard/net-standard#net-implementation-support)上で動作します。</span><span class="sxs-lookup"><span data-stu-id="abc40-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="abc40-130">.NET Core を対象とする利点はいくつかあり、リリースのたびにその利点が増えています。</span><span class="sxs-lookup"><span data-stu-id="abc40-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="abc40-131">.NET Framework 経由による .NET Core には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="abc40-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="abc40-132">クロスプラットフォーム。</span><span class="sxs-lookup"><span data-stu-id="abc40-132">Cross-platform.</span></span> <span data-ttu-id="abc40-133">Windows、macOS、Linux 上で実行される。</span><span class="sxs-lookup"><span data-stu-id="abc40-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="abc40-134">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="abc40-134">Improved performance</span></span>
* [<span data-ttu-id="abc40-135">side-by-side でのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="abc40-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="abc40-136">新しい API</span><span class="sxs-lookup"><span data-stu-id="abc40-136">New APIs</span></span>
* <span data-ttu-id="abc40-137">ソースを開く</span><span class="sxs-lookup"><span data-stu-id="abc40-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="abc40-138">推奨のラーニング パス</span><span class="sxs-lookup"><span data-stu-id="abc40-138">Recommended learning path</span></span>

<span data-ttu-id="abc40-139">ASP.NET Core アプリを開発する場合の概要として、次の順序でチュートリアルを読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="abc40-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="abc40-140">開発または保守管理するアプリの種類に合わせてチュートリアルを選択します。</span><span class="sxs-lookup"><span data-stu-id="abc40-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="abc40-141">アプリの種類</span><span class="sxs-lookup"><span data-stu-id="abc40-141">App type</span></span>  |<span data-ttu-id="abc40-142">シナリオ</span><span class="sxs-lookup"><span data-stu-id="abc40-142">Scenario</span></span>  |<span data-ttu-id="abc40-143">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="abc40-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="abc40-144">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-144">Web app</span></span>                   | <span data-ttu-id="abc40-145">新しいサーバー側 Web UI 開発</span><span class="sxs-lookup"><span data-stu-id="abc40-145">New server-side web UI development</span></span> |<span data-ttu-id="abc40-146">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="abc40-146">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span> |
   |<span data-ttu-id="abc40-147">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-147">Web app</span></span>                   | <span data-ttu-id="abc40-148">MVC アプリの保守管理</span><span class="sxs-lookup"><span data-stu-id="abc40-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="abc40-149">MVC の概要</span><span class="sxs-lookup"><span data-stu-id="abc40-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="abc40-150">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-150">Web app</span></span>                   | <span data-ttu-id="abc40-151">クライアント Web UI 開発</span><span class="sxs-lookup"><span data-stu-id="abc40-151">Client-side web UI development</span></span> |<span data-ttu-id="abc40-152">[Blazor の概要](xref:tutorials/first-blazor-app)</span><span class="sxs-lookup"><span data-stu-id="abc40-152">[Get started with Blazor](xref:tutorials/first-blazor-app)</span></span> |
   |<span data-ttu-id="abc40-153">Web API</span><span class="sxs-lookup"><span data-stu-id="abc40-153">Web API</span></span>                   | <span data-ttu-id="abc40-154">RESTful HTTP サービス</span><span class="sxs-lookup"><span data-stu-id="abc40-154">RESTful HTTP services</span></span> |<span data-ttu-id="abc40-155">[Web API の作成](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="abc40-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="abc40-156">リモート プロシージャ コール アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-156">Remote Procedure Call app</span></span> | <span data-ttu-id="abc40-157">プロトコル バッファーを利用したコントラクト優先サービス</span><span class="sxs-lookup"><span data-stu-id="abc40-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="abc40-158">gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="abc40-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="abc40-159">リアルタイムのアプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-159">Real-time app</span></span>             | <span data-ttu-id="abc40-160">サーバーと接続クライアント間での双方向通信</span><span class="sxs-lookup"><span data-stu-id="abc40-160">Bidirectional communication between servers and connected clients</span></span> |<span data-ttu-id="abc40-161">[SignalR の概要](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="abc40-161">[Get started with SignalR](xref:tutorials/signalr)</span></span> |

1. <span data-ttu-id="abc40-162">基本のデータ アクセスの実行方法を示すチュートリアルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="abc40-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="abc40-163">シナリオ</span><span class="sxs-lookup"><span data-stu-id="abc40-163">Scenario</span></span>  |<span data-ttu-id="abc40-164">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="abc40-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="abc40-165">新しい開発</span><span class="sxs-lookup"><span data-stu-id="abc40-165">New development</span></span>        |<span data-ttu-id="abc40-166">[Entity Framework Core を使用した Razor Pages](xref:data/ef-rp/intro)</span><span class="sxs-lookup"><span data-stu-id="abc40-166">[Razor Pages with Entity Framework Core](xref:data/ef-rp/intro)</span></span> |
   |<span data-ttu-id="abc40-167">MVC アプリの保守管理</span><span class="sxs-lookup"><span data-stu-id="abc40-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="abc40-168">Entity Framework Core を使用した MVC</span><span class="sxs-lookup"><span data-stu-id="abc40-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="abc40-169">すべての種類のアプリに該当する ASP.NET Core の[基本](xref:fundamentals/index)の概要は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="abc40-170">興味のあるその他のトピックは、目次から参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="abc40-171">&dagger;[対話式 Web API チュートリアル](/learn/modules/build-web-api-net-core)もあります。</span><span class="sxs-lookup"><span data-stu-id="abc40-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="abc40-172">開発ツールはローカルにインストールする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="abc40-172">No local installation of development tools is required.</span></span> <span data-ttu-id="abc40-173">このコードは、ブラウザーの [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) で実行し、テストには [curl](https://curl.haxx.se/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="abc40-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="abc40-174">.NET Framework からの移行</span><span class="sxs-lookup"><span data-stu-id="abc40-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="abc40-175">ASP.NET 4.x アプリを ASP.NET Core に移行するためのリファレンス ガイドについては、「<xref:migration/proper-to-2x/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="abc40-176">ASP.NET Core は、インターネットに接続された最新のクラウド対応アプリを構築するための、クロス プラットフォームで高パフォーマンスの[オープン ソース](https://github.com/dotnet/aspnetcore) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="abc40-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="abc40-177">ASP.NET Core では次のことができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="abc40-178">Web アプリ、Web サービス、[モノのインターネット (IoT)](https://www.microsoft.com/internet-of-things/) アプリ、モバイル バックエンドを構築する。</span><span class="sxs-lookup"><span data-stu-id="abc40-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="abc40-179">Windows、macOS、Linux で好みの開発ツールを使う。</span><span class="sxs-lookup"><span data-stu-id="abc40-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="abc40-180">クラウドまたはオンプレミスに展開する。</span><span class="sxs-lookup"><span data-stu-id="abc40-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="abc40-181">[.NET Core または .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上で実行する。</span><span class="sxs-lookup"><span data-stu-id="abc40-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="abc40-182">ASP.NET Core を選ぶ理由</span><span class="sxs-lookup"><span data-stu-id="abc40-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="abc40-183">何百万もの開発者は、[ASP.NET 4.x](/aspnet/overview) を使用して、Web アプリを作成しました。</span><span class="sxs-lookup"><span data-stu-id="abc40-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="abc40-184">ASP.NET Core は ASP.NET 4.x を設計し直したものであり、無駄のないモジュール形式のフレームワークになるようにアーキテクチャが変更されています。</span><span class="sxs-lookup"><span data-stu-id="abc40-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="abc40-185">ASP.NET Core MVC を使って Web API と Web UI を構築する</span><span class="sxs-lookup"><span data-stu-id="abc40-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="abc40-186">ASP.NET Core MVC は、[Web API](xref:tutorials/first-web-api) と [Web アプリ](xref:tutorials/razor-pages/index)を構築する機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="abc40-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="abc40-187">[モデル ビュー コントローラー (MVC) パターン](xref:mvc/overview)は、Web API と Web アプリをテスト可能にするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="abc40-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="abc40-188">[Razor Pages](xref:razor-pages/index) はページ ベースのプログラミング モデルであり、Web UI の開発を容易にし、生産性を高めます。</span><span class="sxs-lookup"><span data-stu-id="abc40-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="abc40-189">[Razor マークアップ](xref:mvc/views/razor)では、[Razor Pages](xref:razor-pages/index) および [MVC ビュー](xref:mvc/views/overview)用に生産性の高い構文が提供されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="abc40-190">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="abc40-191">[複数のデータ形式とコンテンツ ネゴシエーション](xref:web-api/advanced/formatting)の組み込みサポートにより、Web API はブラウザーやモバイル デバイスなどのさまざまなクライアントと接続できます。</span><span class="sxs-lookup"><span data-stu-id="abc40-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="abc40-192">[モデル バインド](xref:mvc/models/model-binding)は、HTTP 要求からアクション メソッドのパラメーターにデータを自動的にマップします。</span><span class="sxs-lookup"><span data-stu-id="abc40-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="abc40-193">[モデル検証](xref:mvc/models/validation)では、クライアント側とサーバー側の検証が自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="abc40-194">クライアント側の開発</span><span class="sxs-lookup"><span data-stu-id="abc40-194">Client-side development</span></span>

<span data-ttu-id="abc40-195">ASP.NET Core は、人気のあるクライアント側のフレームワークとライブラリ ([Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react)、[Bootstrap](https://getbootstrap.com/) など) をシームレスに統合します。</span><span class="sxs-lookup"><span data-stu-id="abc40-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="abc40-196">詳細については、<xref:blazor/index> と "*クライアント側の開発*" の関連トピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="abc40-197">.NET Framework を対象とする ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abc40-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="abc40-198">ASP.NET Core 2.x は、.NET Core または .NET Framework を対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="abc40-199">.NET Framework を対象とする ASP.NET Core アプリはクロスプラットフォームではありません&mdash;Windows でのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="abc40-200">一般に、ASP.NET Core 2.x は [.NET Standard](/dotnet/standard/net-standard) ライブラリで構成されています。</span><span class="sxs-lookup"><span data-stu-id="abc40-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="abc40-201">.NET Standard 2.0 を使って作成されたライブラリは、[.NET Standard 2.0 を実装しているすべての .NET プラットフォーム](/dotnet/standard/net-standard#net-implementation-support)上で動作します。</span><span class="sxs-lookup"><span data-stu-id="abc40-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="abc40-202">ASP.NET Core 2.x は、.NET Standard 2.0 を実装している .NET Framework バージョンにおいてサポートされています。</span><span class="sxs-lookup"><span data-stu-id="abc40-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="abc40-203">.NET Framework の最新バージョンをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="abc40-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="abc40-204">.NET Framework 4.6.1 以降。</span><span class="sxs-lookup"><span data-stu-id="abc40-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="abc40-205">ASP.NET Core 3.0 以降は、.NET Core でのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="abc40-206">この変更に関する詳細については、「[ASP.NET Core 3.0 で導入される変更について](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="abc40-207">.NET Core を対象とする利点はいくつかあり、リリースのたびにその利点が増えています。</span><span class="sxs-lookup"><span data-stu-id="abc40-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="abc40-208">.NET Framework 経由による .NET Core には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="abc40-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="abc40-209">クロスプラットフォームである。</span><span class="sxs-lookup"><span data-stu-id="abc40-209">Cross-platform.</span></span> <span data-ttu-id="abc40-210">macOS、Linux、Windows で実行できる。</span><span class="sxs-lookup"><span data-stu-id="abc40-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="abc40-211">パフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="abc40-211">Improved performance</span></span>
* [<span data-ttu-id="abc40-212">side-by-side でのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="abc40-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="abc40-213">新しい API</span><span class="sxs-lookup"><span data-stu-id="abc40-213">New APIs</span></span>
* <span data-ttu-id="abc40-214">ソースを開く</span><span class="sxs-lookup"><span data-stu-id="abc40-214">Open source</span></span>

<span data-ttu-id="abc40-215">.NET Framework から .NET Core までの API ギャップを埋める目的で、[Windows 互換機能パック](/dotnet/core/porting/windows-compat-pack)により、多くの Windows 限定の API が .NET Core で利用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="abc40-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="abc40-216">このような API は .NET Core 1.x で利用できませんでした。</span><span class="sxs-lookup"><span data-stu-id="abc40-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="abc40-217">推奨のラーニング パス</span><span class="sxs-lookup"><span data-stu-id="abc40-217">Recommended learning path</span></span>

<span data-ttu-id="abc40-218">ASP.NET Core アプリを開発する場合の概要として、次の順序でチュートリアルと記事を読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="abc40-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="abc40-219">開発または管理するアプリの種類別のチュートリアルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="abc40-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="abc40-220">アプリの種類</span><span class="sxs-lookup"><span data-stu-id="abc40-220">App type</span></span>  |<span data-ttu-id="abc40-221">シナリオ</span><span class="sxs-lookup"><span data-stu-id="abc40-221">Scenario</span></span>  |<span data-ttu-id="abc40-222">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="abc40-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="abc40-223">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-223">Web app</span></span>                   | <span data-ttu-id="abc40-224">新規の開発</span><span class="sxs-lookup"><span data-stu-id="abc40-224">For new development</span></span>        |<span data-ttu-id="abc40-225">[Razor Pages の使用を開始する](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="abc40-225">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span> |
   |<span data-ttu-id="abc40-226">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-226">Web app</span></span>                   | <span data-ttu-id="abc40-227">MVC アプリの管理</span><span class="sxs-lookup"><span data-stu-id="abc40-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="abc40-228">MVC の概要</span><span class="sxs-lookup"><span data-stu-id="abc40-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="abc40-229">Web API</span><span class="sxs-lookup"><span data-stu-id="abc40-229">Web API</span></span>                   |                            |<span data-ttu-id="abc40-230">[Web API の作成](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="abc40-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="abc40-231">リアルタイムのアプリ</span><span class="sxs-lookup"><span data-stu-id="abc40-231">Real-time app</span></span>             |                            |<span data-ttu-id="abc40-232">[SignalR の概要](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="abc40-232">[Get started with SignalR](xref:tutorials/signalr)</span></span> |

1. <span data-ttu-id="abc40-233">基本のデータ アクセスの実行方法を示すチュートリアルは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="abc40-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="abc40-234">シナリオ</span><span class="sxs-lookup"><span data-stu-id="abc40-234">Scenario</span></span>  |<span data-ttu-id="abc40-235">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="abc40-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="abc40-236">新規の開発</span><span class="sxs-lookup"><span data-stu-id="abc40-236">For new development</span></span>        |<span data-ttu-id="abc40-237">[Entity Framework Core を使用した Razor Pages](xref:data/ef-rp/intro)</span><span class="sxs-lookup"><span data-stu-id="abc40-237">[Razor Pages with Entity Framework Core](xref:data/ef-rp/intro)</span></span> |
   | <span data-ttu-id="abc40-238">MVC アプリの管理</span><span class="sxs-lookup"><span data-stu-id="abc40-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="abc40-239">Entity Framework Core を使用した MVC</span><span class="sxs-lookup"><span data-stu-id="abc40-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="abc40-240">すべての種類のアプリに該当する ASP.NET Core の[基本](xref:fundamentals/index)の概要は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="abc40-241">興味のあるその他のトピックは、目次から参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="abc40-242">&dagger;[すべてブラウザーでたどる Web API のチュートリアル](/learn/modules/build-web-api-net-core)もあります。ローカルに IDE をインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="abc40-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="abc40-243">このコードは、[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) で実行し、テストには [curl](https://curl.haxx.se/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="abc40-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="abc40-244">.NET Framework からの移行</span><span class="sxs-lookup"><span data-stu-id="abc40-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="abc40-245">ASP.NET アプリを ASP.NET Core に移行するためのリファレンス ガイドについては、「<xref:migration/proper-to-2x/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="abc40-246">サンプルをダウンロードする方法</span><span class="sxs-lookup"><span data-stu-id="abc40-246">How to download a sample</span></span>

<span data-ttu-id="abc40-247">多くの記事やチュートリアルにサンプル コードへのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="abc40-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="abc40-248">[ASP.NET リポジトリの zip ファイルをダウンロード](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master)します。</span><span class="sxs-lookup"><span data-stu-id="abc40-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="abc40-249">*Docs-master.zip* ファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="abc40-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="abc40-250">サンプル リンクの URL を使って、サンプル ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="abc40-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="abc40-251">サンプル コードのプリプロセッサ ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="abc40-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="abc40-252">複数のシナリオを示すため、サンプル アプリでは `#define` と `#if-#else/#elif-#endif` のプリプロセッサ ディレクティブを使用してさまざまなサンプル コードのセクションを選択してコンパイルし、実行します。</span><span class="sxs-lookup"><span data-stu-id="abc40-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="abc40-253">このアプローチを活用するサンプルでは、C# ファイルの上部にある `#define` ディレクティブを設定して、実行するシナリオに関連付けられたシンボルを定義します。</span><span class="sxs-lookup"><span data-stu-id="abc40-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="abc40-254">一部のサンプルでは、シナリオを実行するために複数のファイルの上部でシンボルを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="abc40-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="abc40-255">たとえば、次の `#define` のシンボル一覧は、4 つのシナリオが使用可能である (シンボルごとに 1 つのシナリオ) ことを示しています。</span><span class="sxs-lookup"><span data-stu-id="abc40-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="abc40-256">現在のサンプル構成では `TemplateCode` のシナリオが実行されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="abc40-257">`ExpandDefault` のシナリオを実行するサンプルを変更するには、`ExpandDefault` のシンボルを定義し、残りのシンボルをコメント アウトしたままにします。</span><span class="sxs-lookup"><span data-stu-id="abc40-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="abc40-258">[C# プリプロセッサ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/)を使用してコードのセクションを選択的にコンパイルする方法の詳細については、「[#define (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define)」および「[#if (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="abc40-259">サンプル コードのリージョン</span><span class="sxs-lookup"><span data-stu-id="abc40-259">Regions in sample code</span></span>

<span data-ttu-id="abc40-260">一部のサンプル アプリには、[#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) と [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) の C# ディレクティブに囲まれたコードのセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="abc40-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="abc40-261">ドキュメントのビルド システムによって、レンダリングされたドキュメントのトピックにこのリージョンが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="abc40-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="abc40-262">リージョン名には通常、"snippet" という単語が含まれています。</span><span class="sxs-lookup"><span data-stu-id="abc40-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="abc40-263">次の例は `snippet_WebHostDefaults` という名前のリージョンを示しています。</span><span class="sxs-lookup"><span data-stu-id="abc40-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="abc40-264">先述の C# コード スニペットは、トピックのマークダウン ファイルの次の行で示されています。</span><span class="sxs-lookup"><span data-stu-id="abc40-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="abc40-265">コードを囲む `#region` と `#endregion` のディレクティブは安全に無視 (または削除) することができます。</span><span class="sxs-lookup"><span data-stu-id="abc40-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="abc40-266">トピックで説明されているサンプル シナリオを実行する予定がある場合は、これらのディレクティブ内のコードを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="abc40-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="abc40-267">他のシナリオを試す場合は、自由にコードを変更できます。</span><span class="sxs-lookup"><span data-stu-id="abc40-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="abc40-268">詳細については、「[Contribute to the ASP.NET documentation: Code snippets (ASP.NET に貢献する: コード スニペット)](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="abc40-269">次の手順</span><span class="sxs-lookup"><span data-stu-id="abc40-269">Next steps</span></span>

<span data-ttu-id="abc40-270">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="abc40-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="abc40-271">ASP.NET Core の基礎</span><span class="sxs-lookup"><span data-stu-id="abc40-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="abc40-272">[週 1 回の ASP.NET Community Standup](https://live.asp.net/) では、チームの進行状況とプランが報告され、</span><span class="sxs-lookup"><span data-stu-id="abc40-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="abc40-273">新しいブログやサード パーティ製ソフトウェアが取り上げられています。</span><span class="sxs-lookup"><span data-stu-id="abc40-273">It features new blogs and third-party software.</span></span>
