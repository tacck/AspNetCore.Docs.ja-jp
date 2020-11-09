---
title: ASP.NET Core の要件ハンドラーでの依存関係の挿入
author: rick-anderson
description: 依存関係の挿入を使用して ASP.NET Core アプリに承認要件ハンドラーを挿入する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060262"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="ff77f-103">ASP.NET Core の要件ハンドラーでの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="ff77f-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="ff77f-104">[承認ハンドラーは](xref:security/authorization/policies#handler-registration) 、 [依存関係の挿入](xref:fundamentals/dependency-injection)を使用して構成するときに、サービスコレクションに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff77f-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="ff77f-105">承認ハンドラー内で評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。</span><span class="sxs-lookup"><span data-stu-id="ff77f-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="ff77f-106">承認は、それを解決してコンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff77f-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="ff77f-107">たとえば、ASP を使用します。NET のログ記録インフラストラクチャは、 `ILoggerFactory` ハンドラーに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="ff77f-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="ff77f-108">このようなハンドラーは、次のコードのようになります。</span><span class="sxs-lookup"><span data-stu-id="ff77f-108">Such a handler might look like the following code:</span></span>

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

<span data-ttu-id="ff77f-109">上記のハンドラーは、任意の [サービスの有効期間](/dotnet/core/extensions/dependency-injection#service-lifetimes)で登録できます。</span><span class="sxs-lookup"><span data-stu-id="ff77f-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ff77f-110">次のコードでは、を使用し `AddSingleton` て、前のハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="ff77f-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="ff77f-111">ハンドラーのインスタンスは、アプリの起動時に作成され、DI はコンストラクターに登録されたを挿入し `ILoggerFactory` ます。</span><span class="sxs-lookup"><span data-stu-id="ff77f-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="ff77f-112">Entity Framework を使用するハンドラーをシングルトンとして登録することはできません。</span><span class="sxs-lookup"><span data-stu-id="ff77f-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
