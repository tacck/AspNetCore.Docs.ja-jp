---
title: ASP.NET Core での単純な承認
author: rick-anderson
description: 承認属性を使用して、ASP.NET Core コントローラーとアクションへのアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775636"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="03adb-103">ASP.NET Core での単純な承認</span><span class="sxs-lookup"><span data-stu-id="03adb-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="03adb-104">MVC での承認は、属性`AuthorizeAttribute`とそのさまざまなパラメーターによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="03adb-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="03adb-105">単純に、コントローラーまたは`AuthorizeAttribute`アクションに属性を適用すると、認証されたユーザーに対するコントローラーまたはアクションへのアクセスが制限されます。</span><span class="sxs-lookup"><span data-stu-id="03adb-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="03adb-106">たとえば、次のコードは、へのアクセス`AccountController`を認証されたユーザーに制限します。</span><span class="sxs-lookup"><span data-stu-id="03adb-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="03adb-107">コントローラーではなくアクションに承認を適用する場合は、アクション自体に`AuthorizeAttribute`属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="03adb-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="03adb-108">これで、認証された`Logout`ユーザーのみが関数にアクセスできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03adb-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="03adb-109">属性を使用して`AllowAnonymous` 、認証されていないユーザーによる個々のアクションへのアクセスを許可することもできます。</span><span class="sxs-lookup"><span data-stu-id="03adb-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="03adb-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="03adb-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="03adb-111">これにより、認証済みのユーザー `AccountController`または認証さ`Login`れていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。</span><span class="sxs-lookup"><span data-stu-id="03adb-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="03adb-112">`[AllowAnonymous]`すべての承認ステートメントをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="03adb-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="03adb-113">との`[AllowAnonymous]` `[Authorize]`属性を組み合わせると、 `[Authorize]`属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="03adb-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="03adb-114">たとえば、コントローラーレベルで`[AllowAnonymous]`を適用した場合、 `[Authorize]`同じコントローラー (またはその中のアクション) の属性はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="03adb-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
