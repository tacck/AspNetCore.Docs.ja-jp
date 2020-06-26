---
title: 2.1 から2.2 または3.0 への移行
author: pakrym
description: 2.1 から2.2 または3.0 を使用する non-ASP.NET Core アプリケーションを移行する方法について説明します。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 0c85ca637c1e93bbde93c7d5d12408637476558e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399791"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="23614-103">2.1 から2.2 または3.0 への移行</span><span class="sxs-lookup"><span data-stu-id="23614-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="23614-104">この記事では、 `Microsoft.Extensions.Logging` 2.1 から2.2 または3.0 までを使用する Non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="23614-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="23614-105">2.1 から 2.2</span><span class="sxs-lookup"><span data-stu-id="23614-105">2.1 to 2.2</span></span>

<span data-ttu-id="23614-106">を手動で作成 `ServiceCollection` して、を呼び出し `AddLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="23614-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="23614-107">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="23614-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="23614-108">2.2 の例:</span><span class="sxs-lookup"><span data-stu-id="23614-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="23614-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="23614-109">2.1 to 3.0</span></span>

<span data-ttu-id="23614-110">3.0 では、を使用 `LoggingFactory.Create` します。</span><span class="sxs-lookup"><span data-stu-id="23614-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="23614-111">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="23614-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="23614-112">3.0 の例:</span><span class="sxs-lookup"><span data-stu-id="23614-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="23614-113">その他の資料</span><span class="sxs-lookup"><span data-stu-id="23614-113">Additional resources</span></span>

* <span data-ttu-id="23614-114">[Microsoft... Logging. コンソール NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="23614-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
