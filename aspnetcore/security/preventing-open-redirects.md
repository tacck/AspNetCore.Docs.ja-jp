---
title: ASP.NET Core でのオープンリダイレクト攻撃の防止
author: ardalis
description: ASP.NET Core アプリに対するオープンリダイレクト攻撃を防ぐ方法を示します。
ms.author: riande
ms.date: 07/07/2017
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: 3a58c25bbd54803ce0b8c42a2667222d6e14c050
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021017"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="2145e-103">ASP.NET Core でのオープンリダイレクト攻撃の防止</span><span class="sxs-lookup"><span data-stu-id="2145e-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="2145e-104">クエリ文字列やフォームデータなど、要求によって指定された URL にリダイレクトする web アプリは、悪意のある外部の URL にユーザーをリダイレクトするために改ざんされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2145e-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="2145e-105">この改ざんは、オープンリダイレクト攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2145e-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="2145e-106">アプリケーションロジックが指定した URL にリダイレクトされるたびに、リダイレクト URL が改ざんされていないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2145e-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="2145e-107">ASP.NET Core には、オープンリダイレクト (オープンリダイレクトとも呼ばれます) 攻撃からアプリを保護するための機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2145e-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="2145e-108">オープンリダイレクト攻撃とは何ですか。</span><span class="sxs-lookup"><span data-stu-id="2145e-108">What is an open redirect attack?</span></span>

<span data-ttu-id="2145e-109">Web アプリケーションは、認証を必要とするリソースにアクセスするときに、ユーザーをログインページに頻繁にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="2145e-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="2145e-110">通常、リダイレクトには、 `returnUrl` 最初に要求された URL にユーザーがログインした後に返すことができるように、querystring パラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2145e-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="2145e-111">ユーザーが認証されると、最初に要求した URL にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2145e-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="2145e-112">送信先 URL は要求のクエリ文字列で指定されるため、悪意のあるユーザーが querystring を改ざんする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2145e-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="2145e-113">改ざんされた querystring を使用すると、サイトが外部の悪意のあるサイトにユーザーをリダイレクトする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2145e-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="2145e-114">この手法は、オープンリダイレクト (またはリダイレクト) 攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2145e-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="2145e-115">攻撃の例</span><span class="sxs-lookup"><span data-stu-id="2145e-115">An example attack</span></span>

<span data-ttu-id="2145e-116">悪意のあるユーザーが、悪意のあるユーザーがユーザーの資格情報や機密情報にアクセスできるようにする攻撃を開発することができます。</span><span class="sxs-lookup"><span data-stu-id="2145e-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="2145e-117">攻撃を開始するために、悪意のあるユーザーは、 `returnUrl` URL にクエリ文字列値が追加されたサイトのログインページへのリンクをクリックするようユーザーに仕向けるます。</span><span class="sxs-lookup"><span data-stu-id="2145e-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="2145e-118">たとえば、にログインページを含むのアプリについて考えてみ `contoso.com` `http://contoso.com/Account/LogOn?returnUrl=/Home/About` ます。</span><span class="sxs-lookup"><span data-stu-id="2145e-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="2145e-119">攻撃は、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="2145e-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="2145e-120">ユーザーがの悪意のあるリンクをクリックした `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (2 番目の URL は "contoso**1**.com" で、"contoso.com" ではありません)。</span><span class="sxs-lookup"><span data-stu-id="2145e-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="2145e-121">ユーザーは正常にログインしました。</span><span class="sxs-lookup"><span data-stu-id="2145e-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="2145e-122">(サイトによって) ユーザーは、 `http://contoso1.com/Account/LogOn` (実際のサイトとまったく同じように見える悪意のあるサイト) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2145e-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="2145e-123">ユーザーはもう一度ログインし (悪意のあるサイトに資格情報を提供)、実際のサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="2145e-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="2145e-124">ユーザーは、最初のログイン試行が失敗し、2回目の試行が成功したと判断したと考えられます。</span><span class="sxs-lookup"><span data-stu-id="2145e-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="2145e-125">ユーザーは、資格情報が侵害されたことを認識しない可能性が最も高くなります。</span><span class="sxs-lookup"><span data-stu-id="2145e-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![オープンリダイレクト攻撃プロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="2145e-127">ログインページに加えて、一部のサイトではリダイレクトページまたはエンドポイントが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2145e-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="2145e-128">たとえば、開いているリダイレクトのあるページがアプリにあると `/Home/Redirect` します。</span><span class="sxs-lookup"><span data-stu-id="2145e-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="2145e-129">攻撃者は、たとえば、電子メールのリンクを作成でき `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` ます。</span><span class="sxs-lookup"><span data-stu-id="2145e-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="2145e-130">一般的なユーザーは、URL を確認し、サイト名で始まることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2145e-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="2145e-131">信頼すると、リンクがクリックされます。</span><span class="sxs-lookup"><span data-stu-id="2145e-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="2145e-132">開いているリダイレクトはユーザーをフィッシングサイトに送信します。これは自分のものと同じですが、ユーザーは自分のサイトであると思われるものにログインする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2145e-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="2145e-133">オープンリダイレクト攻撃からの保護</span><span class="sxs-lookup"><span data-stu-id="2145e-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="2145e-134">Web アプリケーションを開発するときは、ユーザーが指定したすべてのデータを信頼できないものとして扱います。</span><span class="sxs-lookup"><span data-stu-id="2145e-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="2145e-135">URL の内容に基づいてユーザーをリダイレクトする機能がアプリケーションにある場合は、そのようなリダイレクトがアプリ内でローカルにのみ実行されることを確認してください (または、クエリ文字列で指定されている可能性のある URL ではなく、既知の URL に対してのみ)。</span><span class="sxs-lookup"><span data-stu-id="2145e-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="2145e-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="2145e-136">LocalRedirect</span></span>

<span data-ttu-id="2145e-137">`LocalRedirect`基底クラスのヘルパーメソッドを使用し `Controller` ます。</span><span class="sxs-lookup"><span data-stu-id="2145e-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="2145e-138">`LocalRedirect`ローカル以外の URL が指定されている場合、は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="2145e-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="2145e-139">それ以外の場合は、メソッドと同じように動作 `Redirect` します。</span><span class="sxs-lookup"><span data-stu-id="2145e-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="2145e-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="2145e-140">IsLocalUrl</span></span>

<span data-ttu-id="2145e-141">リダイレクトする前に Url をテストするには、 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2145e-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="2145e-142">次の例では、リダイレクトの前に URL がローカルかどうかを確認する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2145e-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="2145e-143">メソッドは、 `IsLocalUrl` ユーザーが誤って悪意のあるサイトにリダイレクトされるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="2145e-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="2145e-144">ローカル url が想定されている状況で、ローカル以外の URL を指定した場合に提供された URL の詳細をログに記録できます。</span><span class="sxs-lookup"><span data-stu-id="2145e-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="2145e-145">リダイレクト Url のログ記録は、リダイレクト攻撃の診断に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2145e-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
