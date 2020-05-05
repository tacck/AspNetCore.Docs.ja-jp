---
title: ASP.NET Core でのオープンリダイレクト攻撃の防止
author: ardalis
description: ASP.NET Core アプリに対するオープンリダイレクト攻撃を防ぐ方法を示します。
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774393"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core でのオープンリダイレクト攻撃の防止

クエリ文字列やフォームデータなど、要求によって指定された URL にリダイレクトする web アプリは、悪意のある外部の URL にユーザーをリダイレクトするために改ざんされる可能性があります。 この改ざんは、オープンリダイレクト攻撃と呼ばれます。

アプリケーションロジックが指定した URL にリダイレクトされるたびに、リダイレクト URL が改ざんされていないことを確認する必要があります。 ASP.NET Core には、オープンリダイレクト (オープンリダイレクトとも呼ばれます) 攻撃からアプリを保護するための機能が組み込まれています。

## <a name="what-is-an-open-redirect-attack"></a>オープンリダイレクト攻撃とは何ですか。

Web アプリケーションは、認証を必要とするリソースにアクセスするときに、ユーザーをログインページに頻繁にリダイレクトします。 通常、リダイレクトには`returnUrl` 、最初に要求された URL にユーザーがログインした後に返すことができるように、querystring パラメーターが含まれています。 ユーザーが認証されると、最初に要求した URL にリダイレクトされます。

送信先 URL は要求のクエリ文字列で指定されるため、悪意のあるユーザーが querystring を改ざんする可能性があります。 改ざんされた querystring を使用すると、サイトが外部の悪意のあるサイトにユーザーをリダイレクトする可能性があります。 この手法は、オープンリダイレクト (またはリダイレクト) 攻撃と呼ばれます。

### <a name="an-example-attack"></a>攻撃の例

悪意のあるユーザーが、悪意のあるユーザーがユーザーの資格情報や機密情報にアクセスできるようにする攻撃を開発することができます。 攻撃を開始するために、悪意のあるユーザーは、URL に`returnUrl`クエリ文字列値が追加されたサイトのログインページへのリンクをクリックするようユーザーに仕向けるます。 たとえば、にログインページを含む`contoso.com`のアプリについて考え`http://contoso.com/Account/LogOn?returnUrl=/Home/About`てみます。 攻撃は、次の手順を実行します。

1. ユーザーがの悪意のあるリンク`http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn`をクリックした (2 番目の URL は "contoso**1**.com" で、"contoso.com" ではありません)。
2. ユーザーは正常にログインしました。
3. (サイトによって) ユーザーは、( `http://contoso1.com/Account/LogOn`実際のサイトとまったく同じように見える悪意のあるサイト) にリダイレクトされます。
4. ユーザーはもう一度ログインし (悪意のあるサイトに資格情報を提供)、実際のサイトにリダイレクトされます。

ユーザーは、最初のログイン試行が失敗し、2回目の試行が成功したと判断したと考えられます。 ユーザーは、資格情報が侵害されたことを認識しない可能性が最も高くなります。

![オープンリダイレクト攻撃プロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

ログインページに加えて、一部のサイトではリダイレクトページまたはエンドポイントが提供されます。 たとえば、 `/Home/Redirect`開いているリダイレクトのあるページがアプリにあるとします。 攻撃者は、たとえば、電子メールのリンクを作成でき`[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`ます。 一般的なユーザーは、URL を確認し、サイト名で始まることを確認します。 信頼すると、リンクがクリックされます。 開いているリダイレクトはユーザーをフィッシングサイトに送信します。これは自分のものと同じですが、ユーザーは自分のサイトであると思われるものにログインする可能性があります。

## <a name="protecting-against-open-redirect-attacks"></a>オープンリダイレクト攻撃からの保護

Web アプリケーションを開発するときは、ユーザーが指定したすべてのデータを信頼できないものとして扱います。 URL の内容に基づいてユーザーをリダイレクトする機能がアプリケーションにある場合は、そのようなリダイレクトがアプリ内でローカルにのみ実行されることを確認してください (または、クエリ文字列で指定されている可能性のある URL ではなく、既知の URL に対してのみ)。

### <a name="localredirect"></a>LocalRedirect

基底`Controller`クラス`LocalRedirect`のヘルパーメソッドを使用します。

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect`ローカル以外の URL が指定されている場合、は例外をスローします。 それ以外の場合は、 `Redirect`メソッドと同じように動作します。

### <a name="islocalurl"></a>IsLocalUrl

リダイレクトする前に Url をテストするには、 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)メソッドを使用します。

次の例では、リダイレクトの前に URL がローカルかどうかを確認する方法を示します。

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

メソッド`IsLocalUrl`は、ユーザーが誤って悪意のあるサイトにリダイレクトされるのを防止します。 ローカル url が想定されている状況で、ローカル以外の URL を指定した場合に提供された URL の詳細をログに記録できます。 リダイレクト Url のログ記録は、リダイレクト攻撃の診断に役立ちます。
