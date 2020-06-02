---
<span data-ttu-id="055ef-101">title: ASP.NET Core 作成者での単純な承認: rick anderson description: authorization 属性を使用して、ASP.NET Core コントローラーとアクションへのアクセスを制限する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="055ef-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="055ef-102">ms. author: riande ms。日付: 10/14/2016:</span><span class="sxs-lookup"><span data-stu-id="055ef-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="055ef-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="055ef-103">'Blazor'</span></span>
- <span data-ttu-id="055ef-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="055ef-104">'Identity'</span></span>
- <span data-ttu-id="055ef-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="055ef-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="055ef-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="055ef-106">'Razor'</span></span>
- <span data-ttu-id="055ef-107">' SignalR ' uid: security/authorization/simple</span><span class="sxs-lookup"><span data-stu-id="055ef-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="055ef-108">ASP.NET Core での単純な承認</span><span class="sxs-lookup"><span data-stu-id="055ef-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="055ef-109">ASP.NET Core での承認は <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 、とそのさまざまなパラメーターで制御されます。</span><span class="sxs-lookup"><span data-stu-id="055ef-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="055ef-110">最も単純な形式では、 `[Authorize]` コントローラー、アクション、またはページに属性を適用する Razor ことで、そのコンポーネントへのアクセスが認証されたユーザーに制限されます。</span><span class="sxs-lookup"><span data-stu-id="055ef-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="055ef-111">たとえば、次のコードは、へのアクセスを認証された `AccountController` ユーザーに制限します。</span><span class="sxs-lookup"><span data-stu-id="055ef-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="055ef-112">コントローラーではなくアクションに承認を適用する場合は、 `AuthorizeAttribute` アクション自体に属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="055ef-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="055ef-113">これで、認証されたユーザーのみが関数にアクセスできるようになりました `Logout` 。</span><span class="sxs-lookup"><span data-stu-id="055ef-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="055ef-114">属性を使用して、認証され `AllowAnonymous` ていないユーザーによる個々のアクションへのアクセスを許可することもできます。</span><span class="sxs-lookup"><span data-stu-id="055ef-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="055ef-115">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="055ef-115">For example:</span></span>

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

<span data-ttu-id="055ef-116">これにより、認証済みのユーザー `AccountController` または認証 `Login` されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。</span><span class="sxs-lookup"><span data-stu-id="055ef-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="055ef-117">`[AllowAnonymous]`すべての承認ステートメントをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="055ef-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="055ef-118">`[AllowAnonymous]`との `[Authorize]` 属性を組み合わせると、 `[Authorize]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="055ef-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="055ef-119">たとえば `[AllowAnonymous]` 、コントローラーレベルでを適用した場合、 `[Authorize]` 同じコントローラー (またはその中のアクション) の属性はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="055ef-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
