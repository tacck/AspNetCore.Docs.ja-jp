---
title: ClaimsPrincipal からの移行
author: mjrousos
description: ASP.NET Core で現在認証されているユーザーの id と要求を取得するために、ClaimsPrincipal から移行する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634073"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal からの移行

ASP.NET 4.x プロジェクトでは、 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) を使用して、現在認証されているユーザーの id と要求を取得するのが一般的でした。 ASP.NET Core では、このプロパティは現在設定されていません。 別の方法で現在認証されているユーザーの id を取得するには、それに依存していたコードを更新する必要があります。

## <a name="context-specific-state-instead-of-static-state"></a>静的な状態ではなく、コンテキスト固有の状態

ASP.NET Core を使用する場合、との両方の値が `ClaimsPrincipal.Current` `Thread.CurrentPrincipal` 設定されません。 これらのプロパティはどちらも静的な状態を表しているので、通常は回避 ASP.NET Core ます。 代わりに、ASP.NET Core は [依存関係の注入](xref:fundamentals/dependency-injection) (DI) を使用して、現在のユーザーの id などの依存関係を提供します。 テスト id は簡単に挿入できるため、現在のユーザーの id を DI から取得することも、よりテストが容易になります。

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core アプリで現在のユーザーを取得する

現在の認証されたユーザーを ASP.NET Core の代わりに取得するには、次のようないくつかのオプションがあり `ClaimsPrincipal` `ClaimsPrincipal.Current` ます。

* **コントローラーの基本ユーザー**。 MVC コントローラーは、 [ユーザー](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) プロパティを使用して、現在の認証済みユーザーにアクセスできます。
* **HttpContext. ユーザー**。 現在の `HttpContext` (ミドルウェアなど) にアクセスできるコンポーネントは、現在のユーザーを `ClaimsPrincipal` [HttpContext. user](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)から取得できます。
* **呼び出し元から渡さ**れました。 現在のにアクセスできないライブラリ `HttpContext` は、コントローラーまたはミドルウェアコンポーネントから呼び出されることが多く、現在のユーザーの id を引数として渡すことができます。
* **IHttpContextAccessor**。 ASP.NET Core に移行されるプロジェクトが大きすぎて、現在のユーザーの id を必要なすべての場所に簡単に渡すことができない場合があります。 このような場合は、回避策として [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) を使用できます。 `IHttpContextAccessor` は現在のにアクセスでき `HttpContext` ます (存在する場合)。 DI が使用されている場合は、「」を参照してください <xref:fundamentals/httpcontext> 。 ASP.NET Core の DI ドリブンアーキテクチャで動作するようにまだ更新されていないコードで現在のユーザーの id を取得するための短期的なソリューションは次のようになります。

  * `IHttpContextAccessor`で[Addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793)を呼び出して、DI コンテナーで使用できるように `Startup.ConfigureServices` します。
  * 起動時にのインスタンスを取得 `IHttpContextAccessor` し、静的変数に格納します。 インスタンスは、以前に静的プロパティから現在のユーザーを取得していたコードで使用できるようになります。
  * 現在のユーザーの `ClaimsPrincipal` を使用して取得し `HttpContextAccessor.HttpContext?.User` ます。 このコードが HTTP 要求のコンテキストの外部で使用されている場合、 `HttpContext` は null になります。

最後のオプションでは、 `IHttpContextAccessor` 静的変数に格納されているインスタンスを使用して、静的な依存関係に挿入された依存関係を優先する ASP.NET Core 原則とは逆になります。 代わりに DI からインスタンスを取得することを計画 `IHttpContextAccessor` します。 を使用する大規模な既存の ASP.NET アプリを移行する場合、静的ヘルパーは便利なブリッジになることがあり `ClaimsPrincipal.Current` ます。
