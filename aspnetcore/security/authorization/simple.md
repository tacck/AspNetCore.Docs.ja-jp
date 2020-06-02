---
title: ASP.NET Core 作成者での単純な承認: rick anderson description: authorization 属性を使用して、ASP.NET Core コントローラーとアクションへのアクセスを制限する方法について説明します。
ms. author: riande ms。日付: 10/14/2016:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: security/authorization/simple

---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core での単純な承認

<a name="security-authorization-simple"></a>

ASP.NET Core での承認は <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 、とそのさまざまなパラメーターで制御されます。 最も単純な形式では、 `[Authorize]` コントローラー、アクション、またはページに属性を適用する Razor ことで、そのコンポーネントへのアクセスが認証されたユーザーに制限されます。

たとえば、次のコードは、へのアクセスを認証された `AccountController` ユーザーに制限します。

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

コントローラーではなくアクションに承認を適用する場合は、 `AuthorizeAttribute` アクション自体に属性を適用します。

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

これで、認証されたユーザーのみが関数にアクセスできるようになりました `Logout` 。

属性を使用して、認証され `AllowAnonymous` ていないユーザーによる個々のアクションへのアクセスを許可することもできます。 次に例を示します。

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

これにより、認証済みのユーザー `AccountController` または認証 `Login` されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。

> [!WARNING]
> `[AllowAnonymous]`すべての承認ステートメントをバイパスします。 `[AllowAnonymous]`との `[Authorize]` 属性を組み合わせると、 `[Authorize]` 属性は無視されます。 たとえば `[AllowAnonymous]` 、コントローラーレベルでを適用した場合、 `[Authorize]` 同じコントローラー (またはその中のアクション) の属性はすべて無視されます。
