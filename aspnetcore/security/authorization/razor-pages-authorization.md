---
title: ':::no-loc(Razor)::: ASP.NET Core でのページ承認規則'
author: rick-anderson
description: ユーザーを承認し、匿名ユーザーがページまたはページのフォルダーにアクセスすることを許可する規則を使用して、ページへのアクセスを制御する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060184"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="02914-103">:::no-loc(Razor)::: ASP.NET Core でのページ承認規則</span><span class="sxs-lookup"><span data-stu-id="02914-103">:::no-loc(Razor)::: Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02914-104">ページアプリでアクセスを制御する方法の1つ :::no-loc(Razor)::: は、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="02914-104">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="02914-105">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="02914-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="02914-106">このトピックで説明する規則は、アクセスを制御するための [承認フィルター](xref:mvc/controllers/filters#authorization-filters) を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="02914-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="02914-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="02914-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02914-108">サンプルアプリでは、を使用[ :::no-loc(cookie)::: せず :::no-loc(ASP.NET Core Identity)::: に認証](xref:security/authentication/:::no-loc(cookie):::)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-108">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="02914-109">このトピックで示す概念と例は、を使用するアプリにも同様に適用され :::no-loc(ASP.NET Core Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="02914-109">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="02914-110">を使用するには :::no-loc(ASP.NET Core Identity)::: 、「」のガイダンスに従って <xref:security/authentication/identity> ください。</span><span class="sxs-lookup"><span data-stu-id="02914-110">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="02914-111">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="02914-111">Require authorization to access a page</span></span>

<span data-ttu-id="02914-112"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 指定されたパスのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="02914-113">指定されたパスは、ビューエンジンのパスです。これは、 :::no-loc(Razor)::: 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="02914-113">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="02914-114">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="02914-115">は、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> フィルター属性を使用してページモデルクラスに適用でき `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="02914-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="02914-116">詳細については、「 [承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02914-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="02914-117">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="02914-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="02914-118">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべてのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02914-119">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="02914-119">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="02914-120">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="02914-121">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="02914-121">Require authorization to access an area page</span></span>

<span data-ttu-id="02914-122">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスの領域ページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="02914-123">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="02914-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="02914-124">たとえば、ファイル *領域/ :::no-loc(Identity)::: /Pages/Manage/Accounts.cshtml* のページ名は、/ *manage/Accounts* です。</span><span class="sxs-lookup"><span data-stu-id="02914-124">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="02914-125">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="02914-126">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="02914-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="02914-127">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべての領域にを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="02914-128">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="02914-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="02914-129">たとえば、[区分]、[/]、 *:::no-loc(Identity):::* [ *管理* ] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-129">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="02914-130">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="02914-131">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="02914-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="02914-132">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02914-133">指定されたパスは、ビューエンジンのパスです。これは、 :::no-loc(Razor)::: 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="02914-133">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="02914-134">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="02914-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="02914-135">指定し <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるフォルダー内のすべてのページにを追加するには、規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="02914-136">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="02914-136">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="02914-137">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="02914-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="02914-138">ページのフォルダーで承認が必要であることを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="02914-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="02914-139">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="02914-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="02914-140">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="02914-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="02914-141">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="02914-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="02914-142">との両方 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> がページに適用されると、が優先され、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> アクセスが制御さ <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> れます。</span><span class="sxs-lookup"><span data-stu-id="02914-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02914-143">その他の資料</span><span class="sxs-lookup"><span data-stu-id="02914-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02914-144">ページアプリでアクセスを制御する方法の1つ :::no-loc(Razor)::: は、起動時に承認規則を使用することです。</span><span class="sxs-lookup"><span data-stu-id="02914-144">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="02914-145">これらの規則を使用すると、ユーザーを承認し、匿名ユーザーが個々のページやページのフォルダーにアクセスすることを許可できます。</span><span class="sxs-lookup"><span data-stu-id="02914-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="02914-146">このトピックで説明する規則は、アクセスを制御するための [承認フィルター](xref:mvc/controllers/filters#authorization-filters) を自動的に適用します。</span><span class="sxs-lookup"><span data-stu-id="02914-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="02914-147">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="02914-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02914-148">サンプルアプリでは、を使用[ :::no-loc(cookie)::: せず :::no-loc(ASP.NET Core Identity)::: に認証](xref:security/authentication/:::no-loc(cookie):::)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-148">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="02914-149">このトピックで示す概念と例は、を使用するアプリにも同様に適用され :::no-loc(ASP.NET Core Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="02914-149">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="02914-150">を使用するには :::no-loc(ASP.NET Core Identity)::: 、「」のガイダンスに従って <xref:security/authentication/identity> ください。</span><span class="sxs-lookup"><span data-stu-id="02914-150">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="02914-151">ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="02914-151">Require authorization to access a page</span></span>

<span data-ttu-id="02914-152">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="02914-153">指定されたパスは、ビューエンジンのパスです。これは、 :::no-loc(Razor)::: 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="02914-153">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="02914-154">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizepage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="02914-155">は、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> フィルター属性を使用してページモデルクラスに適用でき `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="02914-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="02914-156">詳細については、「 [承認フィルター属性](xref:razor-pages/filter#authorize-filter-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02914-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="02914-157">ページのフォルダーにアクセスするには承認が必要です</span><span class="sxs-lookup"><span data-stu-id="02914-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="02914-158">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべてのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="02914-159">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="02914-159">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="02914-160">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [authorizefolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="02914-161">区分ページへのアクセスに承認を要求する</span><span class="sxs-lookup"><span data-stu-id="02914-161">Require authorization to access an area page</span></span>

<span data-ttu-id="02914-162">を使用して <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 、指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスの領域ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="02914-163">ページ名は、指定された領域のページルートディレクトリを基準とした拡張子のないファイルのパスです。</span><span class="sxs-lookup"><span data-stu-id="02914-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="02914-164">たとえば、ファイル *領域/ :::no-loc(Identity)::: /Pages/Manage/Accounts.cshtml* のページ名は、/ *manage/Accounts* です。</span><span class="sxs-lookup"><span data-stu-id="02914-164">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="02914-165">[承認ポリシー](xref:security/authorization/policies)を指定するには、次のように、 [Authorizeareapage オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="02914-166">領域のフォルダーにアクセスするための承認が必要</span><span class="sxs-lookup"><span data-stu-id="02914-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="02914-167">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> たパスにあるフォルダー内のすべての領域にを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="02914-168">フォルダーパスは、指定された領域のページルートディレクトリを基準としたフォルダーのパスです。</span><span class="sxs-lookup"><span data-stu-id="02914-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="02914-169">たとえば、[区分]、[/]、 *:::no-loc(Identity):::* [ *管理* ] の下にあるファイルのフォルダーパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-169">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="02914-170">[承認ポリシー](xref:security/authorization/policies)を指定するには、 [AuthorizeAreaFolder オーバーロード](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)を使用します。</span><span class="sxs-lookup"><span data-stu-id="02914-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="02914-171">ページへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="02914-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="02914-172">を使用して <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 、指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="02914-173">指定されたパスは、ビューエンジンのパスです。これは、 :::no-loc(Razor)::: 拡張子のないページルート相対パスで、スラッシュだけを含みます。</span><span class="sxs-lookup"><span data-stu-id="02914-173">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="02914-174">ページのフォルダーへの匿名アクセスを許可する</span><span class="sxs-lookup"><span data-stu-id="02914-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="02914-175">を使用して、 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 指定し <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> たパスにあるフォルダー内のすべてのページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="02914-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="02914-176">指定されたパスは、ページルートの相対パスであるビューエンジンのパスです :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="02914-176">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="02914-177">承認済みアクセスと匿名アクセスの組み合わせに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="02914-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="02914-178">承認を必要とするページのフォルダーを指定し、そのフォルダー内のページで匿名アクセスが許可されるように指定することが有効です。</span><span class="sxs-lookup"><span data-stu-id="02914-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="02914-179">ただし、逆は無効です。</span><span class="sxs-lookup"><span data-stu-id="02914-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="02914-180">匿名アクセス用のページのフォルダーを宣言して、承認が必要なフォルダー内のページを指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="02914-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="02914-181">プライベートページで承認を要求すると失敗します。</span><span class="sxs-lookup"><span data-stu-id="02914-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="02914-182">との両方 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> がページに適用されると、が優先され、 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> アクセスが制御さ <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> れます。</span><span class="sxs-lookup"><span data-stu-id="02914-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02914-183">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="02914-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
