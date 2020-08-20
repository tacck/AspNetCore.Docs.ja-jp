---
title: ASP.NET Core のポリシースキーム
author: rick-anderson
description: 認証ポリシースキームを使用すると、単一の論理認証スキームを簡単に使用できるようになります。
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634983"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core のポリシースキーム

認証ポリシースキームを使用すると、単一の論理認証スキームを使用して、複数の方法を使用することが容易になります。 たとえば、ポリシースキームでは、問題には Google 認証を使用し、それ以外の場合は認証を使用し cookie ます。 認証ポリシーのスキームによって次のようになります。

* 任意の認証アクションを別のスキームに簡単に転送できます。
* 要求に基づいて動的に転送します。

派生 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> と関連付けられた[ \<TOptions> authenticationhandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)を使用するすべての認証方式:

* は、2.1 以降の ASP.NET Core で自動的にポリシースキームになります。
* は、スキームのオプションを構成することによって有効にすることができます。

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>例

次の例は、下位レベルのスキームを結合する上位のスキームを示しています。 Google 認証はチャレンジに使用され、その cookie 他すべてに認証が使用されます。

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

次の例では、要求ごとにスキームを動的に選択できます。 つまり、s と API 認証を混在させる方法を cookie 次に示します。

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
