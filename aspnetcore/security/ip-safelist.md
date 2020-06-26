---
title: ASP.NET Core のクライアント IP セーフセーフ
author: damienbod
description: 承認済み IP アドレスの一覧に対してリモート IP アドレスを検証するミドルウェアまたはアクションフィルターを作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 5b74205bc7b17d61edbb73cf309f6e24e4318391
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409008"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="19572-103">ASP.NET Core のクライアント IP セーフセーフ</span><span class="sxs-lookup"><span data-stu-id="19572-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="19572-104">By [Damien Bowden](https://twitter.com/damien_bod)および[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="19572-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="19572-105">この記事では、ASP.NET Core アプリで IP アドレスセーフリスト (許可一覧とも呼ばれます) を実装する3つの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="19572-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="19572-106">付随するサンプルアプリでは、3つの方法すべてを示しています。</span><span class="sxs-lookup"><span data-stu-id="19572-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="19572-107">使用できるもの:</span><span class="sxs-lookup"><span data-stu-id="19572-107">You can use:</span></span>

* <span data-ttu-id="19572-108">ミドルウェアを使用して、すべての要求のリモート IP アドレスを確認します。</span><span class="sxs-lookup"><span data-stu-id="19572-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="19572-109">特定のコントローラーまたはアクションメソッドに対する要求のリモート IP アドレスを確認するための MVC アクションフィルター。</span><span class="sxs-lookup"><span data-stu-id="19572-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* Razor<span data-ttu-id="19572-110">ページフィルターを使用して、ページの要求のリモート IP アドレスを確認し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="19572-110"> Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="19572-111">どちらの場合も、承認済みのクライアント IP アドレスを含む文字列は、アプリ設定に格納されます。</span><span class="sxs-lookup"><span data-stu-id="19572-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="19572-112">ミドルウェアまたはフィルター:</span><span class="sxs-lookup"><span data-stu-id="19572-112">The middleware or filter:</span></span>

* <span data-ttu-id="19572-113">文字列を解析して配列に格納します。</span><span class="sxs-lookup"><span data-stu-id="19572-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="19572-114">リモート IP アドレスが配列に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="19572-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="19572-115">配列に IP アドレスが含まれている場合、アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="19572-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="19572-116">それ以外の場合は、HTTP 403 の許可されていない状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="19572-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="19572-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="19572-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="19572-118">IP アドレスのセーフセーフ</span><span class="sxs-lookup"><span data-stu-id="19572-118">IP address safelist</span></span>

<span data-ttu-id="19572-119">サンプルアプリでは、IP アドレスのセーフセーフは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="19572-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="19572-120">ファイルのappsettings.jsのプロパティによって定義され `AdminSafeList` ます。 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="19572-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="19572-121">[インターネットプロトコルバージョン 4 (IPv4)](https://wikipedia.org/wiki/IPv4)と[インターネットプロトコルバージョン 6 (IPv6)](https://wikipedia.org/wiki/IPv6)の両方のアドレスを含むことができる、セミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="19572-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="19572-122">前の例では、およびの IPv4 アドレスと、 `127.0.0.1` `192.168.1.5` `::1` (の圧縮形式) の IPv6 ループバックアドレスが許可されてい `0:0:0:0:0:0:0:1` ます。</span><span class="sxs-lookup"><span data-stu-id="19572-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="19572-123">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="19572-123">Middleware</span></span>

<span data-ttu-id="19572-124">メソッドは、 `Startup.Configure` カスタム `AdminSafeListMiddleware` ミドルウェア型をアプリの要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="19572-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="19572-125">セーフセーフは、.NET Core 構成プロバイダーを使用して取得され、コンストラクターパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="19572-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="19572-126">ミドルウェアは、文字列を配列に解析し、配列内のリモート IP アドレスを検索します。</span><span class="sxs-lookup"><span data-stu-id="19572-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="19572-127">リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 403 禁止を返します。</span><span class="sxs-lookup"><span data-stu-id="19572-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="19572-128">この検証プロセスは、HTTP GET 要求ではバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="19572-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="19572-129">アクションフィルター</span><span class="sxs-lookup"><span data-stu-id="19572-129">Action filter</span></span>

<span data-ttu-id="19572-130">特定の MVC コントローラーまたはアクションメソッドに対して、[セーフ] を選択したアクセス制御が必要な場合は、アクションフィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="19572-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="19572-131">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="19572-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="19572-132">で `Startup.ConfigureServices` 、アクションフィルターを MVC フィルターコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="19572-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="19572-133">次の例では、 `ClientIpCheckActionFilter` アクションフィルターを追加します。</span><span class="sxs-lookup"><span data-stu-id="19572-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="19572-134">セーフセーフとコンソールロガーインスタンスは、コンストラクターパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="19572-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="19572-135">次に、 [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性を使用して、アクションフィルターをコントローラーまたはアクションメソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="19572-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="19572-136">サンプルアプリでは、アクションフィルターがコントローラーのアクションメソッドに適用され `Get` ます。</span><span class="sxs-lookup"><span data-stu-id="19572-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="19572-137">以下を送信してアプリをテストする場合:</span><span class="sxs-lookup"><span data-stu-id="19572-137">When you test the app by sending:</span></span>

* <span data-ttu-id="19572-138">HTTP GET 要求では、 `[ServiceFilter]` 属性によってクライアントの IP アドレスが検証されます。</span><span class="sxs-lookup"><span data-stu-id="19572-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="19572-139">アクションメソッドへのアクセスが許可されている場合 `Get` 、次のコンソール出力のバリエーションがアクションフィルターとアクションメソッドによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="19572-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="19572-140">GET 以外の HTTP 要求動詞は、 `AdminSafeListMiddleware` クライアントの IP アドレスを検証します。</span><span class="sxs-lookup"><span data-stu-id="19572-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a>Razor<span data-ttu-id="19572-141">ページフィルター</span><span class="sxs-lookup"><span data-stu-id="19572-141"> Pages filter</span></span>

<span data-ttu-id="19572-142">ページアプリに対してセーフデマンドによるアクセス制御が必要な場合は Razor 、ページフィルターを使用し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="19572-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="19572-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="19572-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="19572-144">で `Startup.ConfigureServices` 、 Razor MVC フィルターコレクションに追加してページフィルターを有効にします。</span><span class="sxs-lookup"><span data-stu-id="19572-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="19572-145">次の例では、 `ClientIpCheckPageFilter` Razor ページフィルターが追加されています。</span><span class="sxs-lookup"><span data-stu-id="19572-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="19572-146">セーフセーフとコンソールロガーインスタンスは、コンストラクターパラメーターとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="19572-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="19572-147">サンプルアプリの*インデックス* Razor ページが要求されると、ページフィルターによって Razor クライアントの IP アドレスが検証されます。</span><span class="sxs-lookup"><span data-stu-id="19572-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="19572-148">フィルターによって、次のようなコンソール出力のバリエーションが生成されます。</span><span class="sxs-lookup"><span data-stu-id="19572-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="19572-149">その他の資料</span><span class="sxs-lookup"><span data-stu-id="19572-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="19572-150">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="19572-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
