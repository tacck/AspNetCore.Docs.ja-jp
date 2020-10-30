---
title: ASP.NET Core の要件ハンドラーでの依存関係の挿入
author: rick-anderson
description: 依存関係の挿入を使用して ASP.NET Core アプリに承認要件ハンドラーを挿入する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060262"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core の要件ハンドラーでの依存関係の挿入

<a name="security-authorization-di"></a>

[承認ハンドラーは](xref:security/authorization/policies#handler-registration) 、 [依存関係の挿入](xref:fundamentals/dependency-injection)を使用して構成するときに、サービスコレクションに登録する必要があります。

承認ハンドラー内で評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。 承認は、それを解決してコンストラクターに挿入します。

たとえば、ASP を使用します。NET のログ記録インフラストラクチャは、 `ILoggerFactory` ハンドラーに挿入されます。 このようなハンドラーは、次のコードのようになります。

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

上記のハンドラーは、任意の [サービスの有効期間](/dotnet/core/extensions/dependency-injection#service-lifetimes)で登録できます。 次のコードでは、を使用し `AddSingleton` て、前のハンドラーを登録します。

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

ハンドラーのインスタンスは、アプリの起動時に作成され、DI はコンストラクターに登録されたを挿入し `ILoggerFactory` ます。

> [!NOTE]
> Entity Framework を使用するハンドラーをシングルトンとして登録することはできません。
