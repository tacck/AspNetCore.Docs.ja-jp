---
title: ASP.NET Core での分散キャッシュ
author: rick-anderson
description: 特にクラウドまたはサーバーファーム環境で、ASP.NET Core 分散キャッシュを使用してアプリのパフォーマンスとスケーラビリティを向上させる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 206ff55aa530cd06c162e49f400b436e9fb9f07a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775298"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="3c7ec-103">ASP.NET Core での分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="3c7ec-104">[Mohsin Nasir](https://github.com/mohsinnasir)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3c7ec-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c7ec-105">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="3c7ec-106">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="3c7ec-107">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="3c7ec-108">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="3c7ec-109">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="3c7ec-110">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="3c7ec-111">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-111">Doesn't use local memory.</span></span>

<span data-ttu-id="3c7ec-112">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="3c7ec-113">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="3c7ec-114">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="3c7ec-115">どの実装が選択されているかにかかわらず、アプリは<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="3c7ec-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c7ec-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c7ec-117">Prerequisites</span></span>

<span data-ttu-id="3c7ec-118">SQL Server 分散キャッシュを使用するには、パッケージ[への参照を追加します](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="3c7ec-119">Redis 分散キャッシュを使用するには、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="3c7ec-120">NCache 分散キャッシュを使用するには、パッケージ参照を[Ncache. Microsoft. Extensions. cache-control. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="3c7ec-121">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-121">IDistributedCache interface</span></span>

<span data-ttu-id="3c7ec-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="3c7ec-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;文字列キーを受け取り、キャッシュ内に存在する場合`byte[]`は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="3c7ec-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 、文字列キーを使用`byte[]`して、キャッシュに項目 (配列として) を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="3c7ec-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 、キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="3c7ec-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 、文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="3c7ec-127">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-127">Establish distributed caching services</span></span>

<span data-ttu-id="3c7ec-128">のの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装をに`Startup.ConfigureServices`登録します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c7ec-129">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="3c7ec-130">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="3c7ec-131">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="3c7ec-132">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="3c7ec-133">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="3c7ec-134">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-134">Distributed Memory Cache</span></span>

<span data-ttu-id="3c7ec-135">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、項目をメモリに格納する<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="3c7ec-136">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="3c7ec-137">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="3c7ec-138">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="3c7ec-139">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="3c7ec-140">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="3c7ec-141">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="3c7ec-142">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="3c7ec-143">このサンプルアプリでは、の`Startup.ConfigureServices`開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="3c7ec-144">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="3c7ec-145">分散型 SQL Server キャッシュの実装<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="3c7ec-146">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、 `sql-cache`ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="3c7ec-147">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="3c7ec-148">`sql-cache create`コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="3c7ec-149">SQL Server`Data Source`インスタンス ()、データベース`Initial Catalog`()、スキーマ (など`dbo`)、テーブル名 (など`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="3c7ec-150">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="3c7ec-151">`sql-cache`ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="3c7ec-153">アプリでは、では<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>なく、のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="3c7ec-154">このサンプルアプリは<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 、の`Startup.ConfigureServices`開発環境以外で実装されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="3c7ec-155">通常<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> 、(およびオプション<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*>で<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>、および) はソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="3c7ec-156">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="3c7ec-157">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-157">Distributed Redis Cache</span></span>

<span data-ttu-id="3c7ec-158">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="3c7ec-159">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="3c7ec-160">アプリでは、の<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices`開発以外の環境でインスタンス () を使用して、キャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="3c7ec-161">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="3c7ec-162">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="3c7ec-163">コマンド`redis-server`プロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="3c7ec-164">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-164">Distributed NCache Cache</span></span>

<span data-ttu-id="3c7ec-165">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="3c7ec-166">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="3c7ec-167">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="3c7ec-168">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="3c7ec-168">To configure NCache:</span></span>

1. <span data-ttu-id="3c7ec-169">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="3c7ec-170">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="3c7ec-171">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="3c7ec-172">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-172">Use the distributed cache</span></span>

<span data-ttu-id="3c7ec-173"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用するには、アプリの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>任意のコンストラクターからのインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="3c7ec-174">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="3c7ec-175">サンプルアプリが起動すると<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、がに`Startup.Configure`挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="3c7ec-176">現在の時刻はを使用<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>してキャッシュされます (詳細については、「 [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="3c7ec-177">サンプルアプリは、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インデックスページ`IndexModel`で使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="3c7ec-178">インデックスページが読み込まれるたびに、キャッシュがで`OnGetAsync`キャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="3c7ec-179">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="3c7ec-180">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="3c7ec-181">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="3c7ec-182">このボタンをクリック`OnPostResetCachedTime`すると、ハンドラーメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="3c7ec-183">インスタンスに対して<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>シングルトンまたはスコープ設定された有効期間を使用する必要はありません (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="3c7ec-184">また、DI を使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>する代わりに、必要に応じてインスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="3c7ec-185">Recommendations</span><span class="sxs-lookup"><span data-stu-id="3c7ec-185">Recommendations</span></span>

<span data-ttu-id="3c7ec-186">アプリに最適なの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装を決定する際には、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="3c7ec-187">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-187">Existing infrastructure</span></span>
* <span data-ttu-id="3c7ec-188">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="3c7ec-188">Performance requirements</span></span>
* <span data-ttu-id="3c7ec-189">[コスト]</span><span class="sxs-lookup"><span data-stu-id="3c7ec-189">Cost</span></span>
* <span data-ttu-id="3c7ec-190">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-190">Team experience</span></span>

<span data-ttu-id="3c7ec-191">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="3c7ec-192">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="3c7ec-193">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="3c7ec-194">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="3c7ec-195">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="3c7ec-196">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c7ec-197">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3c7ec-197">Additional resources</span></span>

* [<span data-ttu-id="3c7ec-198">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="3c7ec-199">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="3c7ec-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="3c7ec-200">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c7ec-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3c7ec-201">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="3c7ec-202">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="3c7ec-203">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="3c7ec-204">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="3c7ec-205">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="3c7ec-206">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="3c7ec-207">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-207">Doesn't use local memory.</span></span>

<span data-ttu-id="3c7ec-208">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="3c7ec-209">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="3c7ec-210">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="3c7ec-211">どの実装が選択されているかにかかわらず、アプリは<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="3c7ec-212">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c7ec-213">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c7ec-213">Prerequisites</span></span>

<span data-ttu-id="3c7ec-214">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="3c7ec-215">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="3c7ec-216">Redis パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="3c7ec-217">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache.. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="3c7ec-218">NCache パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイル内で個別に ncache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="3c7ec-219">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-219">IDistributedCache interface</span></span>

<span data-ttu-id="3c7ec-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="3c7ec-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;文字列キーを受け取り、キャッシュ内に存在する場合`byte[]`は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="3c7ec-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 、文字列キーを使用`byte[]`して、キャッシュに項目 (配列として) を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="3c7ec-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 、キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="3c7ec-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 、文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="3c7ec-225">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-225">Establish distributed caching services</span></span>

<span data-ttu-id="3c7ec-226">のの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装をに`Startup.ConfigureServices`登録します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c7ec-227">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="3c7ec-228">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="3c7ec-229">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="3c7ec-230">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="3c7ec-231">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="3c7ec-232">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-232">Distributed Memory Cache</span></span>

<span data-ttu-id="3c7ec-233">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、項目をメモリに格納する<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="3c7ec-234">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="3c7ec-235">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="3c7ec-236">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="3c7ec-237">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="3c7ec-238">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="3c7ec-239">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="3c7ec-240">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="3c7ec-241">このサンプルアプリでは、の`Startup.ConfigureServices`開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="3c7ec-242">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="3c7ec-243">分散型 SQL Server キャッシュの実装<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="3c7ec-244">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、 `sql-cache`ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="3c7ec-245">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="3c7ec-246">`sql-cache create`コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="3c7ec-247">SQL Server`Data Source`インスタンス ()、データベース`Initial Catalog`()、スキーマ (など`dbo`)、テーブル名 (など`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="3c7ec-248">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="3c7ec-249">`sql-cache`ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="3c7ec-251">アプリでは、では<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>なく、のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="3c7ec-252">このサンプルアプリは<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 、の`Startup.ConfigureServices`開発環境以外で実装されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="3c7ec-253">通常<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> 、(およびオプション<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*>で<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>、および) はソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="3c7ec-254">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="3c7ec-255">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-255">Distributed Redis Cache</span></span>

<span data-ttu-id="3c7ec-256">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="3c7ec-257">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="3c7ec-258">アプリでは、の<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices`開発以外の環境でインスタンス () を使用して、キャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="3c7ec-259">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="3c7ec-260">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="3c7ec-261">コマンド`redis-server`プロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="3c7ec-262">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-262">Distributed NCache Cache</span></span>

<span data-ttu-id="3c7ec-263">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="3c7ec-264">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="3c7ec-265">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="3c7ec-266">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="3c7ec-266">To configure NCache:</span></span>

1. <span data-ttu-id="3c7ec-267">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="3c7ec-268">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="3c7ec-269">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="3c7ec-270">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-270">Use the distributed cache</span></span>

<span data-ttu-id="3c7ec-271"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用するには、アプリの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>任意のコンストラクターからのインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="3c7ec-272">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="3c7ec-273">サンプルアプリが起動すると<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、がに`Startup.Configure`挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="3c7ec-274">現在の時刻はを使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>してキャッシュされます (詳細については、「 [Web Host: iapplicationlifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="3c7ec-275">サンプルアプリは、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インデックスページ`IndexModel`で使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="3c7ec-276">インデックスページが読み込まれるたびに、キャッシュがで`OnGetAsync`キャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="3c7ec-277">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="3c7ec-278">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="3c7ec-279">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="3c7ec-280">このボタンをクリック`OnPostResetCachedTime`すると、ハンドラーメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="3c7ec-281">インスタンスに対して<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>シングルトンまたはスコープ設定された有効期間を使用する必要はありません (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="3c7ec-282">また、DI を使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>する代わりに、必要に応じてインスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="3c7ec-283">Recommendations</span><span class="sxs-lookup"><span data-stu-id="3c7ec-283">Recommendations</span></span>

<span data-ttu-id="3c7ec-284">アプリに最適なの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装を決定する際には、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="3c7ec-285">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-285">Existing infrastructure</span></span>
* <span data-ttu-id="3c7ec-286">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="3c7ec-286">Performance requirements</span></span>
* <span data-ttu-id="3c7ec-287">[コスト]</span><span class="sxs-lookup"><span data-stu-id="3c7ec-287">Cost</span></span>
* <span data-ttu-id="3c7ec-288">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-288">Team experience</span></span>

<span data-ttu-id="3c7ec-289">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="3c7ec-290">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="3c7ec-291">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="3c7ec-292">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="3c7ec-293">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="3c7ec-294">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c7ec-295">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3c7ec-295">Additional resources</span></span>

* [<span data-ttu-id="3c7ec-296">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="3c7ec-297">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="3c7ec-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="3c7ec-298">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c7ec-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3c7ec-299">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="3c7ec-300">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="3c7ec-301">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="3c7ec-302">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="3c7ec-303">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="3c7ec-304">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="3c7ec-305">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-305">Doesn't use local memory.</span></span>

<span data-ttu-id="3c7ec-306">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="3c7ec-307">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="3c7ec-308">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="3c7ec-309">どの実装が選択されているかにかかわらず、アプリは<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="3c7ec-310">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c7ec-311">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c7ec-311">Prerequisites</span></span>

<span data-ttu-id="3c7ec-312">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="3c7ec-313">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し[て、パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)参照をパッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="3c7ec-314">Redis パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="3c7ec-315">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache.. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="3c7ec-316">NCache パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイル内で個別に ncache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="3c7ec-317">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-317">IDistributedCache interface</span></span>

<span data-ttu-id="3c7ec-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="3c7ec-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;文字列キーを受け取り、キャッシュ内に存在する場合`byte[]`は、キャッシュされた項目を配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="3c7ec-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 、文字列キーを使用`byte[]`して、キャッシュに項目 (配列として) を追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="3c7ec-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 、キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="3c7ec-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 、文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="3c7ec-323">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-323">Establish distributed caching services</span></span>

<span data-ttu-id="3c7ec-324">のの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装をに`Startup.ConfigureServices`登録します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c7ec-325">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="3c7ec-326">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="3c7ec-327">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="3c7ec-328">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="3c7ec-329">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="3c7ec-330">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-330">Distributed Memory Cache</span></span>

<span data-ttu-id="3c7ec-331">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、項目をメモリに格納する<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のフレームワーク提供の実装です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="3c7ec-332">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="3c7ec-333">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="3c7ec-334">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="3c7ec-335">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="3c7ec-336">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="3c7ec-337">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="3c7ec-338">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="3c7ec-339">このサンプルアプリでは、の`Startup.ConfigureServices`開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="3c7ec-340">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="3c7ec-341">分散型 SQL Server キャッシュの実装<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="3c7ec-342">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、 `sql-cache`ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="3c7ec-343">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="3c7ec-344">`sql-cache create`コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="3c7ec-345">SQL Server`Data Source`インスタンス ()、データベース`Initial Catalog`()、スキーマ (など`dbo`)、テーブル名 (など`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="3c7ec-346">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="3c7ec-347">`sql-cache`ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="3c7ec-349">アプリでは、では<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>なく、のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="3c7ec-350">このサンプルアプリは<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 、の`Startup.ConfigureServices`開発環境以外で実装されています。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="3c7ec-351">通常<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> 、(およびオプション<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*>で<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>、および) はソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="3c7ec-352">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="3c7ec-353">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-353">Distributed Redis Cache</span></span>

<span data-ttu-id="3c7ec-354">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="3c7ec-355">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="3c7ec-356">アプリは、 <xref:Microsoft.Extensions.Caching.Redis.RedisCache>インスタンス (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) を使用してキャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="3c7ec-357">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="3c7ec-358">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="3c7ec-359">コマンド`redis-server`プロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="3c7ec-360">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-360">Distributed NCache Cache</span></span>

<span data-ttu-id="3c7ec-361">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="3c7ec-362">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="3c7ec-363">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="3c7ec-364">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="3c7ec-364">To configure NCache:</span></span>

1. <span data-ttu-id="3c7ec-365">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="3c7ec-366">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="3c7ec-367">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="3c7ec-368">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="3c7ec-368">Use the distributed cache</span></span>

<span data-ttu-id="3c7ec-369"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用するには、アプリの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>任意のコンストラクターからのインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="3c7ec-370">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="3c7ec-371">サンプルアプリが起動すると<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、がに`Startup.Configure`挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="3c7ec-372">現在の時刻はを使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>してキャッシュされます (詳細については、「 [Web Host: iapplicationlifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="3c7ec-373">サンプルアプリは、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インデックスページ`IndexModel`で使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="3c7ec-374">インデックスページが読み込まれるたびに、キャッシュがで`OnGetAsync`キャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="3c7ec-375">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="3c7ec-376">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="3c7ec-377">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="3c7ec-378">このボタンをクリック`OnPostResetCachedTime`すると、ハンドラーメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="3c7ec-379">インスタンスに対して<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>シングルトンまたはスコープ設定された有効期間を使用する必要はありません (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="3c7ec-380">また、DI を使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>する代わりに、必要に応じてインスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="3c7ec-381">Recommendations</span><span class="sxs-lookup"><span data-stu-id="3c7ec-381">Recommendations</span></span>

<span data-ttu-id="3c7ec-382">アプリに最適なの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装を決定する際には、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="3c7ec-383">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="3c7ec-383">Existing infrastructure</span></span>
* <span data-ttu-id="3c7ec-384">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="3c7ec-384">Performance requirements</span></span>
* <span data-ttu-id="3c7ec-385">[コスト]</span><span class="sxs-lookup"><span data-stu-id="3c7ec-385">Cost</span></span>
* <span data-ttu-id="3c7ec-386">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="3c7ec-386">Team experience</span></span>

<span data-ttu-id="3c7ec-387">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="3c7ec-388">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="3c7ec-389">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="3c7ec-390">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="3c7ec-391">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="3c7ec-392">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3c7ec-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c7ec-393">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3c7ec-393">Additional resources</span></span>

* [<span data-ttu-id="3c7ec-394">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3c7ec-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="3c7ec-395">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="3c7ec-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="3c7ec-396">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c7ec-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 