---
title: ASP.NET Core Blazor のログ
author: guardrex
description: ログ レベルの構成や Razor コンポーネントからログ メッセージを書き込む方法など、Blazor アプリでのログ記録について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103327"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="44dc4-103">ASP.NET Core Blazor のログ</span><span class="sxs-lookup"><span data-stu-id="44dc4-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="44dc4-104"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="44dc4-104"> WebAssembly</span></span>

<span data-ttu-id="44dc4-105">`Program.Main` の `WebAssemblyHostBuilder.Logging` プロパティを使用して、Blazor WebAssembly アプリでのログ記録を構成します。</span><span class="sxs-lookup"><span data-stu-id="44dc4-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="44dc4-106">`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="44dc4-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="44dc4-107">ログの構成は、アプリの設定ファイルから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="44dc4-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="44dc4-108">詳細については、「<xref:blazor/fundamentals/logging>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44dc4-108">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="44dc4-109"> サーバー</span><span class="sxs-lookup"><span data-stu-id="44dc4-109"> Server</span></span>

<span data-ttu-id="44dc4-110">一般的な ASP.NET Core のログ記録のガイダンスについては、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="44dc4-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="44dc4-111"> WebAssembly の SignalR .NET クライアントのログ</span><span class="sxs-lookup"><span data-stu-id="44dc4-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="44dc4-112"><xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入して、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> に渡されたログ プロバイダーに `WebAssemblyConsoleLogger` を追加します。</span><span class="sxs-lookup"><span data-stu-id="44dc4-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="44dc4-113">従来の <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> とは異なり、`WebAssemblyConsoleLogger` はブラウザー固有のログ API (例: `console.log`) のラッパーです。</span><span class="sxs-lookup"><span data-stu-id="44dc4-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="44dc4-114">`WebAssemblyConsoleLogger` を使用すると、ブラウザー コンテキスト内の Mono 内でログ記録できるようになります。</span><span class="sxs-lookup"><span data-stu-id="44dc4-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="44dc4-115">Razor コンポーネントでのログ</span><span class="sxs-lookup"><span data-stu-id="44dc4-115">Log in Razor components</span></span>

<span data-ttu-id="44dc4-116">ロガーは、アプリのスタートアップ構成を尊重します。</span><span class="sxs-lookup"><span data-stu-id="44dc4-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="44dc4-117"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の Intellisense 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。</span><span class="sxs-lookup"><span data-stu-id="44dc4-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="44dc4-118">次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILogger> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="44dc4-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="44dc4-119">次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="44dc4-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="44dc4-120">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="44dc4-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
