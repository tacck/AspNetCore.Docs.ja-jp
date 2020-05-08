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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776501"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core のクライアント IP セーフセーフ

By [Damien Bowden](https://twitter.com/damien_bod)および[Tom Dykstra](https://github.com/tdykstra)
 
この記事では、ASP.NET Core アプリで IP アドレスセーフリスト (許可一覧とも呼ばれます) を実装する3つの方法を示します。 付随するサンプルアプリでは、3つの方法すべてを示しています。 使用できるもの:

* ミドルウェアを使用して、すべての要求のリモート IP アドレスを確認します。
* 特定のコントローラーまたはアクションメソッドに対する要求のリモート IP アドレスを確認するための MVC アクションフィルター。
* Razorページフィルターを使用して、ページのRazor要求のリモート IP アドレスを確認します。

どちらの場合も、承認済みのクライアント IP アドレスを含む文字列は、アプリ設定に格納されます。 ミドルウェアまたはフィルター:

* 文字列を解析して配列に格納します。 
* リモート IP アドレスが配列に存在するかどうかを確認します。

配列に IP アドレスが含まれている場合、アクセスが許可されます。 それ以外の場合は、HTTP 403 の許可されていない状態コードが返されます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="ip-address-safelist"></a>IP アドレスのセーフセーフ

サンプルアプリでは、IP アドレスのセーフセーフは次のとおりです。

* Appsettings ファイルの`AdminSafeList`プロパティによっ*appsettings.json*て定義されます。
* [インターネットプロトコルバージョン 4 (IPv4)](https://wikipedia.org/wiki/IPv4)と[インターネットプロトコルバージョン 6 (IPv6)](https://wikipedia.org/wiki/IPv6)の両方のアドレスを含むことができる、セミコロンで区切られた文字列。

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

前の例では`127.0.0.1` 、および`192.168.1.5`の IPv4 アドレスと、(の`::1` `0:0:0:0:0:0:0:1`圧縮形式) の IPv6 ループバックアドレスが許可されています。

## <a name="middleware"></a>ミドルウェア

メソッド`Startup.Configure`は、カスタム`AdminSafeListMiddleware`ミドルウェア型をアプリの要求パイプラインに追加します。 セーフセーフは、.NET Core 構成プロバイダーを使用して取得され、コンストラクターパラメーターとして渡されます。

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

ミドルウェアは、文字列を配列に解析し、配列内のリモート IP アドレスを検索します。 リモート IP アドレスが見つからない場合、ミドルウェアは HTTP 403 禁止を返します。 この検証プロセスは、HTTP GET 要求ではバイパスされます。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>アクションフィルター

特定の MVC コントローラーまたはアクションメソッドに対して、[セーフ] を選択したアクセス制御が必要な場合は、アクションフィルターを使用します。 次に例を示します。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

で`Startup.ConfigureServices`、アクションフィルターを MVC フィルターコレクションに追加します。 次の例では、 `ClientIpCheckActionFilter`アクションフィルターを追加します。 セーフセーフとコンソールロガーインスタンスは、コンストラクターパラメーターとして渡されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

次に、 [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性を使用して、アクションフィルターをコントローラーまたはアクションメソッドに適用できます。

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

サンプルアプリでは、アクションフィルターがコントローラーの`Get`アクションメソッドに適用されます。 以下を送信してアプリをテストする場合:

* HTTP GET 要求では、 `[ServiceFilter]`属性によってクライアントの IP アドレスが検証されます。 `Get`アクションメソッドへのアクセスが許可されている場合、次のコンソール出力のバリエーションがアクションフィルターとアクションメソッドによって生成されます。

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET `AdminSafeListMiddleware`以外の HTTP 要求動詞は、クライアントの IP アドレスを検証します。

## <a name="razor-pages-filter"></a>Razorページフィルター

Razorページアプリに対してセーフデマンドによるアクセス制御が必要な場合Razorは、ページフィルターを使用します。 次に例を示します。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

で`Startup.ConfigureServices`、MVC フィルター Razorコレクションに追加してページフィルターを有効にします。 次の例では、 `ClientIpCheckPageFilter` Razorページフィルターが追加されています。 セーフセーフとコンソールロガーインスタンスは、コンストラクターパラメーターとして渡されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

サンプルアプリの*インデックス* Razorページが要求されると、 Razorページフィルターによってクライアントの IP アドレスが検証されます。 フィルターによって、次のようなコンソール出力のバリエーションが生成されます。

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/middleware/index>
* [アクション フィルター](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
