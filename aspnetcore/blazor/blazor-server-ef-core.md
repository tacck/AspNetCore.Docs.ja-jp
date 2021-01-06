---
title: ASP.NET Core Blazor Server と Entity Framework Core (EFCore)
author: JeremyLikness
description: Blazor Server アプリで EF Core を使用するためのガイダンスです。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 10fc0afe84065f2c226d1e9c2f4314142369613a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97011885"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="cc758-103">ASP.NET Core Blazor Server と Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="cc758-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="cc758-104">By (フィルター方法):[Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="cc758-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cc758-105">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="cc758-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="cc758-106">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="cc758-107">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="cc758-108">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc758-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="cc758-109">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="cc758-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="cc758-110">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="cc758-111">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="cc758-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="cc758-112">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="cc758-112">Sample app</span></span></h2>

<span data-ttu-id="cc758-113">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="cc758-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="cc758-114">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc758-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="cc758-115">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="cc758-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc758-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc758-117">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="cc758-118">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="cc758-119">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="cc758-120">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="cc758-121">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="cc758-122">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="cc758-123">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="cc758-124">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="cc758-124">Database access</span></span></h2>

<span data-ttu-id="cc758-125">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="cc758-126">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="cc758-127">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、スコープ サービスの登録が問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc758-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="cc758-128"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="cc758-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="cc758-129">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="cc758-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="cc758-130">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="cc758-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="cc758-131">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="cc758-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="cc758-132">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="cc758-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="cc758-133">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="cc758-134">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="cc758-135">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="cc758-136">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="cc758-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="cc758-137">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="cc758-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="cc758-138">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="cc758-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="cc758-139">新しい DbContext インスタンス</span><span class="sxs-lookup"><span data-stu-id="cc758-139">New DbContext instances</span></span></h3>

<span data-ttu-id="cc758-140">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="cc758-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="cc758-141">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="cc758-142">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="cc758-143">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc758-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="cc758-144">EF Core 5.0 以降には、新しいコンテキストを作成するためのファクトリが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc758-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="cc758-145">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc758-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="cc758-146">このコードでは、[拡張メソッド (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) を使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="cc758-147">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="cc758-148">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc758-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="cc758-149">`Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="cc758-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="cc758-150">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="cc758-151">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、[[ASP.NET Core の Identity モデル]](xref:security/authentication/customize_identity_model) を使用する場合など、`DbContext` ごとに接続文字列を構成できるファクトリを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="cc758-152">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="cc758-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="cc758-153">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="cc758-154">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="cc758-155">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="cc758-156">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="cc758-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="cc758-157">サンプル アプリでは、コンポーネントが破棄されるときに、コンテキストも確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="cc758-158">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="cc758-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="cc758-159">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="cc758-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="cc758-160">機密データのログ記録を有効にする</span><span class="sxs-lookup"><span data-stu-id="cc758-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="cc758-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> では、例外メッセージおよびフレームワークのログにアプリケーション データが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc758-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="cc758-162">ログに記録されるデータには、エンティティ インスタンスのプロパティに割り当てられた値と、データベースに送信されたコマンドのパラメーター値を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="cc758-163"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> を使用したデータのログ記録は **セキュリティ リスク** です。データベースに対して実行された SQL ステートメントをログに記録するときに、パスワードやその他の個人を特定できる情報 (PII) が公開される可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="cc758-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="cc758-164"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> は、開発とテストのためにのみ有効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc758-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="cc758-165">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="cc758-165">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="cc758-166">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="cc758-167">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="cc758-168">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="cc758-168">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="cc758-169">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="cc758-169">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="cc758-170">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-170">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="cc758-171">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="cc758-171">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="cc758-172">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="cc758-172">Sample app</span></span></h2>

<span data-ttu-id="cc758-173">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="cc758-173">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="cc758-174">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc758-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="cc758-175">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="cc758-176">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="cc758-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc758-177">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="cc758-178">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="cc758-179">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="cc758-180">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="cc758-181">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="cc758-182">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="cc758-183">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="cc758-184">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="cc758-184">Database access</span></span></h2>

<span data-ttu-id="cc758-185">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="cc758-186">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="cc758-187">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、これは問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc758-187">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="cc758-188"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="cc758-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="cc758-189">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="cc758-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="cc758-190">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="cc758-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="cc758-191">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="cc758-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="cc758-192">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="cc758-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="cc758-193">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-193">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="cc758-194">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="cc758-195">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="cc758-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="cc758-196">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="cc758-196">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="cc758-197">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="cc758-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="cc758-198">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="cc758-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="cc758-199">新しい DbContext インスタンス</span><span class="sxs-lookup"><span data-stu-id="cc758-199">New DbContext instances</span></span></h3>

<span data-ttu-id="cc758-200">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="cc758-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="cc758-201">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="cc758-202">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="cc758-203">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc758-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="cc758-204">このサンプル アプリでは、`Data/DbContextFactory.cs` で独自のファクトリを実装しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="cc758-205">前のファクトリは、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="cc758-205">In the preceding factory:</span></span>

* <span data-ttu-id="cc758-206">すべての依存関係は、サービス プロバイダーを介して <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> によって満たされています。</span><span class="sxs-lookup"><span data-stu-id="cc758-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="cc758-207">`IDbContextFactory` は、EF Core ASP.NET Core 5.0 以降で使用できます。そのため、インターフェイスは、[ASP.NET Core 3.x 用のサンプル アプリに実装されます](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)。</span><span class="sxs-lookup"><span data-stu-id="cc758-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="cc758-208">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc758-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="cc758-209">このコードでは、拡張メソッドを使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="cc758-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="cc758-210">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="cc758-211">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc758-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="cc758-212">`Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="cc758-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="cc758-213">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc758-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="cc758-214">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、[ASP.NET Core の Identity モデル])(xref:security/authentication/customize_identity_model) を使用する場合など、`DbContext` ごとに接続文字列を構成できるファクトリを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="cc758-215">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="cc758-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="cc758-216">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="cc758-217">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="cc758-218">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="cc758-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="cc758-219">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="cc758-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="cc758-220">サンプル アプリでは、コンポーネントが破棄されるときに、コンテキストも確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="cc758-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="cc758-221">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="cc758-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="cc758-222">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="cc758-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="cc758-223">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="cc758-223">In the preceding example:</span></span>

* <span data-ttu-id="cc758-224">`Busy` を `true` に設定すると、非同期操作が開始されることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc758-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="cc758-225">`Busy` を `false` に戻すと、非同期操作は完了するはずです。</span><span class="sxs-lookup"><span data-stu-id="cc758-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="cc758-226">追加のエラー処理ロジックを `catch` ブロックに配置します。</span><span class="sxs-lookup"><span data-stu-id="cc758-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="cc758-227">機密データのログ記録を有効にする</span><span class="sxs-lookup"><span data-stu-id="cc758-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="cc758-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> では、例外メッセージおよびフレームワークのログにアプリケーション データが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc758-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="cc758-229">ログに記録されるデータには、エンティティ インスタンスのプロパティに割り当てられた値と、データベースに送信されたコマンドのパラメーター値を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cc758-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="cc758-230"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> を使用したデータのログ記録は **セキュリティ リスク** です。データベースに対して実行された SQL ステートメントをログに記録するときに、パスワードやその他の個人を特定できる情報 (PII) が公開される可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="cc758-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="cc758-231"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> は、開発とテストのためにのみ有効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc758-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cc758-232">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="cc758-232">Additional resources</span></span>

* [<span data-ttu-id="cc758-233">EF Core のドキュメント</span><span class="sxs-lookup"><span data-stu-id="cc758-233">EF Core documentation</span></span>](/ef/)
