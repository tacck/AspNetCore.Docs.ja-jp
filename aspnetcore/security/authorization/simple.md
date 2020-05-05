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
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core での単純な承認

<a name="security-authorization-simple"></a>

MVC での承認は、属性`AuthorizeAttribute`とそのさまざまなパラメーターによって制御されます。 単純に、コントローラーまたは`AuthorizeAttribute`アクションに属性を適用すると、認証されたユーザーに対するコントローラーまたはアクションへのアクセスが制限されます。

たとえば、次のコードは、へのアクセス`AccountController`を認証されたユーザーに制限します。

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

コントローラーではなくアクションに承認を適用する場合は、アクション自体に`AuthorizeAttribute`属性を適用します。

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

これで、認証された`Logout`ユーザーのみが関数にアクセスできるようになりました。

属性を使用して`AllowAnonymous` 、認証されていないユーザーによる個々のアクションへのアクセスを許可することもできます。 次に例を示します。

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

これにより、認証済みのユーザー `AccountController`または認証さ`Login`れていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。

> [!WARNING]
> `[AllowAnonymous]`すべての承認ステートメントをバイパスします。 との`[AllowAnonymous]` `[Authorize]`属性を組み合わせると、 `[Authorize]`属性は無視されます。 たとえば、コントローラーレベルで`[AllowAnonymous]`を適用した場合、 `[Authorize]`同じコントローラー (またはその中のアクション) の属性はすべて無視されます。
