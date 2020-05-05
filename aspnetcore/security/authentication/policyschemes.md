---
title: ASP.NET Core のポリシースキーム
author: rick-anderson
description: 認証ポリシースキームを使用すると、単一の論理認証スキームを簡単に使用できるようになります。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767318"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core のポリシースキーム

認証ポリシースキームを使用すると、単一の論理認証スキームを使用して、複数の方法を使用することが容易になります。 たとえば、ポリシースキームは、問題には Google 認証を使用し、それ以外の場合は cookie 認証を使用します。 認証ポリシーのスキームによって次のようになります。

* 任意の認証アクションを別のスキームに簡単に転送できます。
* 要求に基づいて動的に転送します。

派生<xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions>と関連付けられている[authenticationhandler\<TOptions](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)を使用するすべての認証方式は、次の>ます。

* は、2.1 以降の ASP.NET Core で自動的にポリシースキームになります。
* は、スキームのオプションを構成することによって有効にすることができます。

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>例

次の例は、下位レベルのスキームを結合する上位のスキームを示しています。 Google 認証はチャレンジに使用され、その他のすべてに cookie 認証が使用されます。

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

次の例では、要求ごとにスキームを動的に選択できます。 つまり、cookie と API 認証を混在させる方法は次のようになります。

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
