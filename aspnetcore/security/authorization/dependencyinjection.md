---
title: ASP.NET Core の要件ハンドラーでの依存関係の挿入
author: rick-anderson
description: 依存関係の挿入を使用して ASP.NET Core アプリに承認要件ハンドラーを挿入する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021004"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="c148d-103">ASP.NET Core の要件ハンドラーでの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="c148d-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="c148d-104">[承認ハンドラーは](xref:security/authorization/policies#handler-registration)、構成中に ([依存関係の挿入](xref:fundamentals/dependency-injection)を使用して) サービスコレクションに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c148d-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="c148d-105">承認ハンドラー内で評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。</span><span class="sxs-lookup"><span data-stu-id="c148d-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="c148d-106">承認は、それを解決してコンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="c148d-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="c148d-107">たとえば、ASP を使用する場合を考えてみます。ハンドラーに挿入する NET のログ記録インフラストラクチャ `ILoggerFactory` 。</span><span class="sxs-lookup"><span data-stu-id="c148d-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="c148d-108">このようなハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c148d-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="c148d-109">ハンドラーは次のように登録し `services.AddSingleton()` ます。</span><span class="sxs-lookup"><span data-stu-id="c148d-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="c148d-110">アプリケーションの起動時にハンドラーのインスタンスが作成され、DI によって、登録されたがコンストラクターに挿入され `ILoggerFactory` ます。</span><span class="sxs-lookup"><span data-stu-id="c148d-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="c148d-111">Entity Framework を使用するハンドラーをシングルトンとして登録することはできません。</span><span class="sxs-lookup"><span data-stu-id="c148d-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
