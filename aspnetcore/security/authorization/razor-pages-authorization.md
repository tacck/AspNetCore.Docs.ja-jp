---
title: RazorASP.NET Core でのページ承認規則
author: rick-anderson
description: ユーザーを承認し、匿名ユーザーがページまたはページのフォルダーにアクセスすることを許可する規則を使用して、ページへのアクセスを制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0f4022e46993c6a957d3d0c133b3db56fa650edc
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568822"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="9e042-103">ASP.NET Core でのページ承認規則</span><span class="sxs-lookup"><span data-stu-id="9e042-103"> Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e042-104">ページアプリでアクセスを制御する方法の1つ Razor は、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="9e042-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="9e042-105">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="9e042-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="9e042-106">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="9e042-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9e042-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9e042-108">このサンプルアプリでは、 [ASP.NET Core Identity を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="9e042-109">このトピックで示す概念と例は、ASP.NET Core を使用するアプリにも同様に適用され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="9e042-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="9e042-110">ASP.NET Core を使用するには Identity 、「」のガイダンスに従って <xref:security/authentication/identity> ください。</span><span class="sxs-lookup"><span data-stu-id="9e042-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="9e042-111">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="9e042-111">Require authorization to access a page</span></span>

<span data-ttu-id="9e042-112"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 指定されたパスのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="9e042-113">指定されたパスは、ビューエンジンのパスです。これは、 Razor 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="9e042-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="9e042-114">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="9e042-115">は、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> フィルター属性を使用してページモデルクラスに適用でき `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="9e042-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="9e042-116">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e042-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="9e042-117">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="9e042-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="9e042-118">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべてのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="9e042-119">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e042-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="9e042-120">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="9e042-121">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="9e042-121">Require authorization to access an area page</span></span>

<span data-ttu-id="9e042-122">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスの領域ページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="9e042-123">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="9e042-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="9e042-124">たとえば、ファイル*領域/ Identity /Pages/Manage/Accounts.cshtml*のページ名は、/ *manage/Accounts*です。</span><span class="sxs-lookup"><span data-stu-id="9e042-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="9e042-125">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="9e042-126">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="9e042-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="9e042-127">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべての領域にを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="9e042-128">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="9e042-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="9e042-129">たとえば、[区分]、[/]、 \* Identity \* [*管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="9e042-130">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="9e042-131">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="9e042-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="9e042-132">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="9e042-133">指定されたパスは、ビューエンジンのパスです。これは、 Razor 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="9e042-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="9e042-134">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="9e042-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="9e042-135">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるフォルダー内のすべてのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="9e042-136">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e042-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="9e042-137">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="9e042-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="9e042-138">ページのフォルダーで承認が必要であることを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="9e042-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="9e042-139">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="9e042-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="9e042-140">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="9e042-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="9e042-141">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="9e042-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="9e042-142">との両方 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> がページに適用されると、が優先され、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> アクセスが制御さ <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> れます。</span><span class="sxs-lookup"><span data-stu-id="9e042-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e042-143">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="9e042-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e042-144">ページアプリでアクセスを制御する方法の1つ Razor は、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="9e042-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="9e042-145">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="9e042-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="9e042-146">このトピックで説明する規則は、アクセスを制御するための[承認フィルター](xref:mvc/controllers/filters#authorization-filters)を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="9e042-147">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9e042-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9e042-148">このサンプルアプリでは、 [ASP.NET Core Identity を使用せずに cookie 認証](xref:security/authentication/cookie)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="9e042-149">このトピックで示す概念と例は、ASP.NET Core を使用するアプリにも同様に適用され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="9e042-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="9e042-150">ASP.NET Core を使用するには Identity 、「」のガイダンスに従って <xref:security/authentication/identity> ください。</span><span class="sxs-lookup"><span data-stu-id="9e042-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="9e042-151">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="9e042-151">Require authorization to access a page</span></span>

<span data-ttu-id="9e042-152">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="9e042-153">指定されたパスは、ビューエンジンのパスです。これは、 Razor 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="9e042-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="9e042-154">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="9e042-155">は、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> フィルター属性を使用してページモデルクラスに適用でき `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="9e042-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="9e042-156">詳細については、「[承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e042-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="9e042-157">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="9e042-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="9e042-158">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべてのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="9e042-159">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e042-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="9e042-160">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="9e042-161">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="9e042-161">Require authorization to access an area page</span></span>

<span data-ttu-id="9e042-162">を使用して <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスの領域ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="9e042-163">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="9e042-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="9e042-164">たとえば、ファイル*領域/ Identity /Pages/Manage/Accounts.cshtml*のページ名は、/ *manage/Accounts*です。</span><span class="sxs-lookup"><span data-stu-id="9e042-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="9e042-165">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="9e042-166">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="9e042-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="9e042-167">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべての領域にを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="9e042-168">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="9e042-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="9e042-169">たとえば、[区分]、[/]、 \* Identity \* [*管理*] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="9e042-170">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e042-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="9e042-171">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="9e042-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="9e042-172">を使用して <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 、指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="9e042-173">指定されたパスは、ビューエンジンのパスです。これは、 Razor 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="9e042-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="9e042-174">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="9e042-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="9e042-175">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるフォルダー内のすべてのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e042-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="9e042-176">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e042-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="9e042-177">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="9e042-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="9e042-178">承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="9e042-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="9e042-179">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="9e042-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="9e042-180">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="9e042-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="9e042-181">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="9e042-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="9e042-182">との両方 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> がページに適用されると、が優先され、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> アクセスが制御さ <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> れます。</span><span class="sxs-lookup"><span data-stu-id="9e042-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e042-183">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="9e042-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
