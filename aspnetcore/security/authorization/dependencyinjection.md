---
title: ASP.NET Core の要件ハンドラーでの依存関係の挿入
author: rick-anderson
description: 依存関係の挿入を使用して ASP.NET Core アプリに承認要件ハンドラーを挿入する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 4bc7eb38262c8a94a84aacc978737a778bfd71a1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632565"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core の要件ハンドラーでの依存関係の挿入

<a name="security-authorization-di"></a>

[承認ハンドラーは](xref:security/authorization/policies#handler-registration) 、構成中に ( [依存関係の挿入](xref:fundamentals/dependency-injection)を使用して) サービスコレクションに登録する必要があります。

承認ハンドラー内で評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。 承認は、それを解決してコンストラクターに挿入します。

たとえば、ASP を使用する場合を考えてみます。ハンドラーに挿入する NET のログ記録インフラストラクチャ `ILoggerFactory` 。 このようなハンドラーは次のようになります。

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

ハンドラーは次のように登録し `services.AddSingleton()` ます。

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

アプリケーションの起動時にハンドラーのインスタンスが作成され、DI によって、登録されたがコンストラクターに挿入され `ILoggerFactory` ます。

> [!NOTE]
> Entity Framework を使用するハンドラーをシングルトンとして登録することはできません。
