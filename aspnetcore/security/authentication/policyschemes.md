---
title: ASP.NET Core のポリシースキーム
author: rick-anderson
description: 認証ポリシースキームを使用すると、単一の論理認証スキームを簡単に使用できるようになります。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053229"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="a5528-103">ASP.NET Core のポリシースキーム</span><span class="sxs-lookup"><span data-stu-id="a5528-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="a5528-104">認証ポリシースキームを使用すると、単一の論理認証スキームを使用して、複数の方法を使用することが容易になります。</span><span class="sxs-lookup"><span data-stu-id="a5528-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="a5528-105">たとえば、ポリシースキームでは、問題には Google 認証を使用し、それ以外の場合は認証を使用し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="a5528-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="a5528-106">認証ポリシーのスキームによって次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a5528-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="a5528-107">任意の認証アクションを別のスキームに簡単に転送できます。</span><span class="sxs-lookup"><span data-stu-id="a5528-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="a5528-108">要求に基づいて動的に転送します。</span><span class="sxs-lookup"><span data-stu-id="a5528-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="a5528-109">派生 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> と関連付けられた[ \<TOptions> authenticationhandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)を使用するすべての認証方式:</span><span class="sxs-lookup"><span data-stu-id="a5528-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="a5528-110">は、2.1 以降の ASP.NET Core で自動的にポリシースキームになります。</span><span class="sxs-lookup"><span data-stu-id="a5528-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="a5528-111">は、スキームのオプションを構成することによって有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a5528-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="a5528-112">例</span><span class="sxs-lookup"><span data-stu-id="a5528-112">Examples</span></span>

<span data-ttu-id="a5528-113">次の例は、下位レベルのスキームを結合する上位のスキームを示しています。</span><span class="sxs-lookup"><span data-stu-id="a5528-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="a5528-114">Google 認証はチャレンジに使用され、その cookie 他すべてに認証が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a5528-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="a5528-115">次の例では、要求ごとにスキームを動的に選択できます。</span><span class="sxs-lookup"><span data-stu-id="a5528-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="a5528-116">つまり、s と API 認証を混在させる方法を cookie 次に示します。</span><span class="sxs-lookup"><span data-stu-id="a5528-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
