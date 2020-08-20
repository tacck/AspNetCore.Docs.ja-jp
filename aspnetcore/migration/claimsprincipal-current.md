---
title: ClaimsPrincipal からの移行
author: mjrousos
description: ASP.NET Core で現在認証されているユーザーの id と要求を取得するために、ClaimsPrincipal から移行する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634073"
---
# <a name="migrate-from-claimsprincipalcurrent"></a><span data-ttu-id="c522b-103">ClaimsPrincipal からの移行</span><span class="sxs-lookup"><span data-stu-id="c522b-103">Migrate from ClaimsPrincipal.Current</span></span>

<span data-ttu-id="c522b-104">ASP.NET 4.x プロジェクトでは、 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) を使用して、現在認証されているユーザーの id と要求を取得するのが一般的でした。</span><span class="sxs-lookup"><span data-stu-id="c522b-104">In ASP.NET 4.x projects, it was common to use [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) to retrieve the current authenticated user's identity and claims.</span></span> <span data-ttu-id="c522b-105">ASP.NET Core では、このプロパティは現在設定されていません。</span><span class="sxs-lookup"><span data-stu-id="c522b-105">In ASP.NET Core, this property is no longer set.</span></span> <span data-ttu-id="c522b-106">別の方法で現在認証されているユーザーの id を取得するには、それに依存していたコードを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c522b-106">Code that was depending on it needs to be updated to get the current authenticated user's identity through a different means.</span></span>

## <a name="context-specific-state-instead-of-static-state"></a><span data-ttu-id="c522b-107">静的な状態ではなく、コンテキスト固有の状態</span><span class="sxs-lookup"><span data-stu-id="c522b-107">Context-specific state instead of static state</span></span>

<span data-ttu-id="c522b-108">ASP.NET Core を使用する場合、との両方の値が `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` 設定されません。</span><span class="sxs-lookup"><span data-stu-id="c522b-108">When using ASP.NET Core, the values of both `ClaimsPrincipal.Current` and `Thread.CurrentPrincipal` aren't set.</span></span> <span data-ttu-id="c522b-109">これらのプロパティはどちらも静的な状態を表しているので、通常は回避 ASP.NET Core ます。</span><span class="sxs-lookup"><span data-stu-id="c522b-109">These properties both represent static state, which ASP.NET Core generally avoids.</span></span> <span data-ttu-id="c522b-110">代わりに、ASP.NET Core は [依存関係の注入](xref:fundamentals/dependency-injection) (DI) を使用して、現在のユーザーの id などの依存関係を提供します。</span><span class="sxs-lookup"><span data-stu-id="c522b-110">Instead, ASP.NET Core uses [dependency injection](xref:fundamentals/dependency-injection) (DI) to provide dependencies such as the current user's identity.</span></span> <span data-ttu-id="c522b-111">テスト id は簡単に挿入できるため、現在のユーザーの id を DI から取得することも、よりテストが容易になります。</span><span class="sxs-lookup"><span data-stu-id="c522b-111">Getting the current user's identity from DI is more testable, too, since test identities can be easily injected.</span></span>

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a><span data-ttu-id="c522b-112">ASP.NET Core アプリで現在のユーザーを取得する</span><span class="sxs-lookup"><span data-stu-id="c522b-112">Retrieve the current user in an ASP.NET Core app</span></span>

<span data-ttu-id="c522b-113">現在の認証されたユーザーを ASP.NET Core の代わりに取得するには、次のようないくつかのオプションがあり `ClaimsPrincipal` `ClaimsPrincipal.Current` ます。</span><span class="sxs-lookup"><span data-stu-id="c522b-113">There are several options for retrieving the current authenticated user's `ClaimsPrincipal` in ASP.NET Core in place of `ClaimsPrincipal.Current`:</span></span>

* <span data-ttu-id="c522b-114">**コントローラーの基本ユーザー**。</span><span class="sxs-lookup"><span data-stu-id="c522b-114">**ControllerBase.User**.</span></span> <span data-ttu-id="c522b-115">MVC コントローラーは、 [ユーザー](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) プロパティを使用して、現在の認証済みユーザーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="c522b-115">MVC controllers can access the current authenticated user with their [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) property.</span></span>
* <span data-ttu-id="c522b-116">**HttpContext. ユーザー**。</span><span class="sxs-lookup"><span data-stu-id="c522b-116">**HttpContext.User**.</span></span> <span data-ttu-id="c522b-117">現在の `HttpContext` (ミドルウェアなど) にアクセスできるコンポーネントは、現在のユーザーを `ClaimsPrincipal` [HttpContext. user](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)から取得できます。</span><span class="sxs-lookup"><span data-stu-id="c522b-117">Components with access to the current `HttpContext` (middleware, for example) can get the current user's `ClaimsPrincipal` from [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).</span></span>
* <span data-ttu-id="c522b-118">**呼び出し元から渡さ**れました。</span><span class="sxs-lookup"><span data-stu-id="c522b-118">**Passed in from caller**.</span></span> <span data-ttu-id="c522b-119">現在のにアクセスできないライブラリ `HttpContext` は、コントローラーまたはミドルウェアコンポーネントから呼び出されることが多く、現在のユーザーの id を引数として渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c522b-119">Libraries without access to the current `HttpContext` are often called from controllers or middleware components and can have the current user's identity passed as an argument.</span></span>
* <span data-ttu-id="c522b-120">**IHttpContextAccessor**。</span><span class="sxs-lookup"><span data-stu-id="c522b-120">**IHttpContextAccessor**.</span></span> <span data-ttu-id="c522b-121">ASP.NET Core に移行されるプロジェクトが大きすぎて、現在のユーザーの id を必要なすべての場所に簡単に渡すことができない場合があります。</span><span class="sxs-lookup"><span data-stu-id="c522b-121">The project being migrated to ASP.NET Core may be too large to easily pass the current user's identity to all necessary locations.</span></span> <span data-ttu-id="c522b-122">このような場合は、回避策として [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c522b-122">In such cases, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) can be used as a workaround.</span></span> <span data-ttu-id="c522b-123">`IHttpContextAccessor` は現在のにアクセスでき `HttpContext` ます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="c522b-123">`IHttpContextAccessor` is able to access the current `HttpContext` (if one exists).</span></span> <span data-ttu-id="c522b-124">DI が使用されている場合は、「」を参照してください <xref:fundamentals/httpcontext> 。</span><span class="sxs-lookup"><span data-stu-id="c522b-124">If DI is being used, see <xref:fundamentals/httpcontext>.</span></span> <span data-ttu-id="c522b-125">ASP.NET Core の DI ドリブンアーキテクチャで動作するようにまだ更新されていないコードで現在のユーザーの id を取得するための短期的なソリューションは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c522b-125">A short-term solution to getting the current user's identity in code that hasn't yet been updated to work with ASP.NET Core's DI-driven architecture would be:</span></span>

  * <span data-ttu-id="c522b-126">`IHttpContextAccessor`で[Addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793)を呼び出して、DI コンテナーで使用できるように `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="c522b-126">Make `IHttpContextAccessor` available in the DI container by calling [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.</span></span>
  * <span data-ttu-id="c522b-127">起動時にのインスタンスを取得 `IHttpContextAccessor` し、静的変数に格納します。</span><span class="sxs-lookup"><span data-stu-id="c522b-127">Get an instance of `IHttpContextAccessor` during startup and store it in a static variable.</span></span> <span data-ttu-id="c522b-128">インスタンスは、以前に静的プロパティから現在のユーザーを取得していたコードで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c522b-128">The instance is made available to code that was previously retrieving the current user from a static property.</span></span>
  * <span data-ttu-id="c522b-129">現在のユーザーの `ClaimsPrincipal` を使用して取得し `HttpContextAccessor.HttpContext?.User` ます。</span><span class="sxs-lookup"><span data-stu-id="c522b-129">Retrieve the current user's `ClaimsPrincipal` using `HttpContextAccessor.HttpContext?.User`.</span></span> <span data-ttu-id="c522b-130">このコードが HTTP 要求のコンテキストの外部で使用されている場合、 `HttpContext` は null になります。</span><span class="sxs-lookup"><span data-stu-id="c522b-130">If this code is used outside of the context of an HTTP request, the `HttpContext` is null.</span></span>

<span data-ttu-id="c522b-131">最後のオプションでは、 `IHttpContextAccessor` 静的変数に格納されているインスタンスを使用して、静的な依存関係に挿入された依存関係を優先する ASP.NET Core 原則とは逆になります。</span><span class="sxs-lookup"><span data-stu-id="c522b-131">The final option, using an `IHttpContextAccessor` instance stored in a static variable, is contrary to the ASP.NET Core principle of preferring injected dependencies to static dependencies.</span></span> <span data-ttu-id="c522b-132">代わりに DI からインスタンスを取得することを計画 `IHttpContextAccessor` します。</span><span class="sxs-lookup"><span data-stu-id="c522b-132">Plan to eventually retrieve `IHttpContextAccessor` instances from DI instead.</span></span> <span data-ttu-id="c522b-133">を使用する大規模な既存の ASP.NET アプリを移行する場合、静的ヘルパーは便利なブリッジになることがあり `ClaimsPrincipal.Current` ます。</span><span class="sxs-lookup"><span data-stu-id="c522b-133">A static helper can be a useful bridge, though, when migrating large existing ASP.NET apps that use `ClaimsPrincipal.Current`.</span></span>
