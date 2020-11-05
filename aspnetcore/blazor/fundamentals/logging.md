---
title: 'ASP.NET Core :::no-loc(Blazor)::: のログ'
author: guardrex
description: 'ログ レベルの構成や :::no-loc(Razor)::: コンポーネントからログ メッセージを書き込む方法など、:::no-loc(Blazor)::: アプリでのログ記録について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055985"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="11504-103">ASP.NET Core :::no-loc(Blazor)::: のログ</span><span class="sxs-lookup"><span data-stu-id="11504-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="11504-104">`Program.Main` の `WebAssemblyHostBuilder.Logging` プロパティを使用して、:::no-loc(Blazor WebAssembly)::: アプリでのログ記録を構成します。</span><span class="sxs-lookup"><span data-stu-id="11504-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="11504-105">`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="11504-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="11504-106">ログの構成は、アプリの設定ファイルから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="11504-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="11504-107">詳細については、「<xref:blazor/fundamentals/configuration#logging-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="11504-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="11504-108">一般的な ASP.NET Core のログ記録のガイダンスについては、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="11504-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="11504-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET クライアント ログ</span><span class="sxs-lookup"><span data-stu-id="11504-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="11504-110"><xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入して、<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> に渡されたログ プロバイダーに `WebAssemblyConsoleLogger` を追加します。</span><span class="sxs-lookup"><span data-stu-id="11504-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="11504-111">従来の <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> とは異なり、`WebAssemblyConsoleLogger` はブラウザー固有のログ API (例: `console.log`) のラッパーです。</span><span class="sxs-lookup"><span data-stu-id="11504-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="11504-112">`WebAssemblyConsoleLogger` を使用すると、ブラウザー コンテキスト内の Mono 内でログ記録できるようになります。</span><span class="sxs-lookup"><span data-stu-id="11504-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="11504-113">:::no-loc(Razor)::: コンポーネントでのログ</span><span class="sxs-lookup"><span data-stu-id="11504-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="11504-114">ロガーは、アプリのスタートアップ構成を尊重します。</span><span class="sxs-lookup"><span data-stu-id="11504-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="11504-115"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の Intellisense 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。</span><span class="sxs-lookup"><span data-stu-id="11504-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="11504-116">次の例は、:::no-loc(Razor)::: コンポーネントでの <xref:Microsoft.Extensions.Logging.ILogger> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="11504-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

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

<span data-ttu-id="11504-117">次の例は、:::no-loc(Razor)::: コンポーネントでの <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="11504-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="11504-118">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="11504-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
