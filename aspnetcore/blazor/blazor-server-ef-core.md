---
title: ASP.NET Core Blazor Server と Entity Framework Core (EFCore)
author: JeremyLikness
description: Blazor Server アプリで EF Core を使用するためのガイダンスです。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: b71b742c8a60b4b563649baa181b8c332ff02501
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865193"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="3ed6c-103">ASP.NET Core Blazor Server と Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="3ed6c-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="3ed6c-104">By (フィルター方法):[Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="3ed6c-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3ed6c-105">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="3ed6c-106">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="3ed6c-107">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="3ed6c-108">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="3ed6c-109">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="3ed6c-110">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="3ed6c-111">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="3ed6c-112">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="3ed6c-112">Sample app</span></span>

<span data-ttu-id="3ed6c-113">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="3ed6c-114">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="3ed6c-115">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="3ed6c-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3ed6c-117">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="3ed6c-118">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="3ed6c-119">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="3ed6c-120">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="3ed6c-121">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="3ed6c-122">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="3ed6c-123">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="3ed6c-124">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="3ed6c-124">Database access</span></span>

<span data-ttu-id="3ed6c-125">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="3ed6c-126">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="3ed6c-127">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、スコープ サービスの登録が問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="3ed6c-128"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="3ed6c-129">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="3ed6c-130">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="3ed6c-131">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="3ed6c-132">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="3ed6c-133">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="3ed6c-134">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="3ed6c-135">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="3ed6c-136">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="3ed6c-137">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="3ed6c-138">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="3ed6c-139">新しい DbContext インスタンス</span><span class="sxs-lookup"><span data-stu-id="3ed6c-139">New DbContext instances</span></span>

<span data-ttu-id="3ed6c-140">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="3ed6c-141">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="3ed6c-142">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="3ed6c-143">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="3ed6c-144">EF Core 5.0 以降には、新しいコンテキストを作成するためのファクトリが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="3ed6c-145">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="3ed6c-146">このコードでは、拡張メソッドを使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="3ed6c-147">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="3ed6c-148">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="3ed6c-149">![NOTE] `Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-149">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="3ed6c-150">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="3ed6c-151">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="3ed6c-151">Scope to the component lifetime</span></span>

<span data-ttu-id="3ed6c-152">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="3ed6c-153">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="3ed6c-154">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="3ed6c-155">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="3ed6c-156">サンプル アプリでは、コンポーネントが破棄されるときに連絡先も確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-156">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="3ed6c-157">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="3ed6c-158">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="3ed6c-159">Blazor Server はステートフル アプリ フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="3ed6c-160">アプリではサーバーへの継続的な接続が維持され、ユーザーの状態は "*回線*" 内のサーバーのメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="3ed6c-161">ユーザー状態の一例として、回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータがあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="3ed6c-162">Blazor Server が提供する独自のアプリケーション モデルでは、Entity Framework Core を使用するための特別なアプローチが必要です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="3ed6c-163">この記事では、Blazor Server アプリでの EF Core について説明します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="3ed6c-164">Blazor WebAssembly アプリは、ほとんどの直接データベース接続が防止される WebAssembly サンドボックス内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="3ed6c-165">Blazor WebAssembly での EF Core の実行については、この記事では扱いません。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="3ed6c-166">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="3ed6c-166">Sample app</span></span>

<span data-ttu-id="3ed6c-167">このサンプル アプリは、EF Core を使用する Blazor Server アプリのリファレンスとして作成されました。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="3ed6c-168">サンプル アプリには、並べ替えとフィルター処理、削除、追加、更新の各操作を行うグリッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="3ed6c-169">このサンプルは、EF Core を使用してオプティミスティック同時実行制御を処理する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="3ed6c-170">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3ed6c-171">サンプルにはローカルの [SQLite](https://www.sqlite.org/index.html) データベースが使用されているため、どのプラットフォームでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="3ed6c-172">このサンプルでは、生成された SQL クエリを表示するようにデータベースのログも構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="3ed6c-173">これは `appsettings.Development.json` で構成されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="3ed6c-174">グリッド、追加、および表示の各コンポーネントでは、操作ごとにコンテキストが作成される "操作ごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="3ed6c-175">編集コンポーネントでは、コンポーネントごとにコンテキストが作成される "コンポーネントごとのコンテキスト" パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="3ed6c-176">このトピックのコード例には、表示されていない名前空間とサービスを必要とするものもあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="3ed6c-177">Razor の例に必要な [`@using`](xref:mvc/views/razor#using) および [`@inject`](xref:mvc/views/razor#inject) ディレクティブを含む完全に機能するコードを検査するには、[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="3ed6c-178">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="3ed6c-178">Database access</span></span>

<span data-ttu-id="3ed6c-179">EF Core では、[データベース アクセスを構成](/ef/core/miscellaneous/configuring-dbcontext)し、"[*作業単位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)" として機能する手段として <xref:Microsoft.EntityFrameworkCore.DbContext> を利用しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="3ed6c-180">EF Core には、既定でコンテキストを "*スコープ*" サービスとして登録する ASP.NET Core アプリの <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 拡張機能が用意されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="3ed6c-181">Blazor Server アプリでは、インスタンスがユーザーの回線内のコンポーネント全体で共有されるため、これは問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="3ed6c-182"><xref:Microsoft.EntityFrameworkCore.DbContext> はスレッド セーフではなく、同時に使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="3ed6c-183">次の理由により、既存の有効期間は不適切です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="3ed6c-184">**[Singleton]\(シングルトン\)** の場合、アプリのすべてのユーザーで状態が共有され、不適切な同時使用につながります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="3ed6c-185">**[範囲指定]** (既定値) の場合、同じユーザーのコンポーネント間で同様の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="3ed6c-186">**[一時的]** の場合、要求ごとに新しいインスタンスが生成されます。ただし、コンポーネントの有効期間が長くなる可能性があるため、意図したよりも時間のかかるコンテキストになります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="3ed6c-187">データベース アクセス</span><span class="sxs-lookup"><span data-stu-id="3ed6c-187">Database access</span></span>

<span data-ttu-id="3ed6c-188">以下の推奨事項は、Blazor Server アプリで EF Core を使用する上で一貫したアプローチを提供するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="3ed6c-189">既定では、操作ごとに 1 つのコンテキストを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="3ed6c-190">コンテキストは、高速でオーバーヘッドの少ないインスタンス化を目的として設計されています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="3ed6c-191">フラグを使用して、複数の同時操作を防止します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-191">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="3ed6c-192">`try` ブロックの `Loading = true;` 行の後に操作を配置します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="3ed6c-193">EF Core の[変更追跡](/ef/core/querying/tracking)または[同時実行制御](/ef/core/saving/concurrency)を利用する、より長期間の操作の場合は、[コンポーネントの有効期間にコンテキストの範囲を限定します](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="3ed6c-194">新しい DbContext インスタンスを作成する</span><span class="sxs-lookup"><span data-stu-id="3ed6c-194">Create new DbContext instances</span></span>

<span data-ttu-id="3ed6c-195">新しい <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを作成する最も簡単な方法は、`new` を使用して新しいインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="3ed6c-196">ただし、追加の依存関係を解決する必要があるシナリオがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="3ed6c-197">たとえば、[`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) を使用してコンテキストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="3ed6c-198">依存関係を持つ新しい <xref:Microsoft.EntityFrameworkCore.DbContext> を作成するには、ファクトリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="3ed6c-199">このサンプル アプリでは、`Data/DbContextFactory.cs` で独自のファクトリを実装しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="3ed6c-200">前のファクトリの <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> では、サービス プロバイダーを介してすべての依存関係を満たしています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="3ed6c-201">次の例では、[SQLite](https://www.sqlite.org/index.html) を構成し、データのログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="3ed6c-202">このコードでは、拡張メソッドを使用して、DI のデータベース ファクトリを構成し、既定のオプションを指定しています。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="3ed6c-203">ファクトリはコンポーネントに挿入され、新しいインスタンスを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="3ed6c-204">たとえば、`Pages/Index.razor` では次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="3ed6c-205">![NOTE] `Wrapper` は、`GridWrapper` コンポーネントへの[コンポーネント参照](xref:blazor/components/index#capture-references-to-components)です。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-205">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="3ed6c-206">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)の `Index` コンポーネント (`Pages/Index.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="3ed6c-207">コンポーネントの有効期間の範囲</span><span class="sxs-lookup"><span data-stu-id="3ed6c-207">Scope to the component lifetime</span></span>

<span data-ttu-id="3ed6c-208">コンポーネントの有効期間中は存在する <xref:Microsoft.EntityFrameworkCore.DbContext> を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="3ed6c-209">これにより、それを[作業単位](https://martinfowler.com/eaaCatalog/unitOfWork.html)として使用し、変更の追跡や同時実行の解決などの組み込み機能を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="3ed6c-210">ファクトリーを使用してコンテキストを作成し、コンポーネントの有効期間中はそれを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="3ed6c-211">まず <xref:System.IDisposable> を実装し、`Pages/EditContact.razor` に示すようにファクトリを挿入します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="3ed6c-212">サンプル アプリでは、コンポーネントが破棄されるときに連絡先も確実に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-212">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="3ed6c-213">最後に、[`OnInitializedAsync`](xref:blazor/components/lifecycle) は新しいコンテキストを作成するためにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="3ed6c-214">サンプル アプリでは、[`OnInitializedAsync`](xref:blazor/components/lifecycle) を使用して同じメソッドで連絡先を読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="3ed6c-215">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="3ed6c-215">In the preceding example:</span></span>

* <span data-ttu-id="3ed6c-216">`Busy` を `true` に設定すると、非同期操作が開始されることがあります。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="3ed6c-217">`Busy` を `false` に戻すと、非同期操作は完了するはずです。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="3ed6c-218">追加のエラー処理ロジックを `catch` ブロックに配置します。</span><span class="sxs-lookup"><span data-stu-id="3ed6c-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3ed6c-219">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3ed6c-219">Additional resources</span></span>

> [<span data-ttu-id="3ed6c-220">EF Core のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3ed6c-220">EF Core documentation</span></span>](/ef/)
