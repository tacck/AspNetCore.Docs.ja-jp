---
title: ASP.NET Core MVC でのビューベースの承認
author: rick-anderson
description: このドキュメントでは、ASP.NET Core ビュー内で承認サービスを挿入および利用する方法について説明し Razor ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: 775ebdffe2b0753de18bf07d9ff1193235a45b17
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629887"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="52014-103">ASP.NET Core MVC でのビューベースの承認</span><span class="sxs-lookup"><span data-stu-id="52014-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="52014-104">多くの場合、開発者は、現在のユーザー id に基づいて UI を表示、非表示にする、または変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="52014-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="52014-105">MVC ビュー内の承認サービスには、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="52014-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="52014-106">認証サービスをビューに挿入するには Razor 、ディレクティブを使用し `@inject` ます。</span><span class="sxs-lookup"><span data-stu-id="52014-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="52014-107">すべてのビューで承認サービスを使用する場合は、 `@inject` ディレクティブを*Views*ディレクトリの *_ViewImports*ファイルに配置します。</span><span class="sxs-lookup"><span data-stu-id="52014-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="52014-108">詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="52014-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="52014-109">挿入された承認サービスを使用して、 `AuthorizeAsync` [リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="52014-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="52014-110">場合によっては、リソースがビューモデルになります。</span><span class="sxs-lookup"><span data-stu-id="52014-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="52014-111">`AuthorizeAsync`[リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="52014-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="52014-112">前のコードでは、モデルはポリシーの評価で考慮する必要があるリソースとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="52014-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="52014-113">唯一の承認チェックとして、アプリの UI 要素の表示の切り替えに依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="52014-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="52014-114">UI 要素を非表示にしても、関連付けられているコントローラーアクションへのアクセスを完全に防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="52014-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="52014-115">たとえば、前のコードスニペットのボタンについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="52014-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="52014-116">ユーザー `Edit` は、相対リソース URL が */Document/Edit/1*であることを知っていれば、アクションメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="52014-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="52014-117">このため、 `Edit` アクションメソッドは独自の承認チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="52014-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
