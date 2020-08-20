---
title: 2.1 から2.2 または3.0 への移行
author: pakrym
description: 2.1 から2.2 または3.0 を使用する non-ASP.NET Core アプリケーションを移行する方法について説明します。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634099"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="8f238-103">2.1 から2.2 または3.0 への移行</span><span class="sxs-lookup"><span data-stu-id="8f238-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="8f238-104">この記事では、 `Microsoft.Extensions.Logging` 2.1 から2.2 または3.0 までを使用する Non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="8f238-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="8f238-105">2.1 から 2.2</span><span class="sxs-lookup"><span data-stu-id="8f238-105">2.1 to 2.2</span></span>

<span data-ttu-id="8f238-106">を手動で作成 `ServiceCollection` して、を呼び出し `AddLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="8f238-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="8f238-107">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="8f238-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8f238-108">2.2 の例:</span><span class="sxs-lookup"><span data-stu-id="8f238-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="8f238-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="8f238-109">2.1 to 3.0</span></span>

<span data-ttu-id="8f238-110">3.0 では、を使用 `LoggingFactory.Create` します。</span><span class="sxs-lookup"><span data-stu-id="8f238-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="8f238-111">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="8f238-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8f238-112">3.0 の例:</span><span class="sxs-lookup"><span data-stu-id="8f238-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="8f238-113">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8f238-113">Additional resources</span></span>

* <span data-ttu-id="8f238-114">[Microsoft... Logging. コンソール NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="8f238-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
