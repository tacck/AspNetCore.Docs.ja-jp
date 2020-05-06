---
title: 2.1 から2.2 または3.0 への移行
author: pakrym
description: 2.1 から2.2 または3.0 を使用する non-ASP.NET Core アプリケーションを移行する方法について説明します。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777060"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="5363b-103">2.1 から2.2 または3.0 への移行</span><span class="sxs-lookup"><span data-stu-id="5363b-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="5363b-104">この記事では、2.1 から2.2 または3.0 までを使用`Microsoft.Extensions.Logging`する non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="5363b-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="5363b-105">2.1 から 2.2</span><span class="sxs-lookup"><span data-stu-id="5363b-105">2.1 to 2.2</span></span>

<span data-ttu-id="5363b-106">を手動`ServiceCollection`で作成`AddLogging`して、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5363b-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="5363b-107">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="5363b-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="5363b-108">2.2 の例:</span><span class="sxs-lookup"><span data-stu-id="5363b-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="5363b-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="5363b-109">2.1 to 3.0</span></span>

<span data-ttu-id="5363b-110">3.0 では、 `LoggingFactory.Create`を使用します。</span><span class="sxs-lookup"><span data-stu-id="5363b-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="5363b-111">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="5363b-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="5363b-112">3.0 の例:</span><span class="sxs-lookup"><span data-stu-id="5363b-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="5363b-113">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5363b-113">Additional resources</span></span>

<xref:fundamentals/logging/index>