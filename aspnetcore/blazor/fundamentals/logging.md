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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 1f4b18bdea02016fb76b75dd01a8fcbeab9b2bc9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402833"
---
# <a name="aspnet-core-blazor-logging"></a>ASP.NET Core Blazor のログ

## Blazor WebAssembly

`Program.Main` の `WebAssemblyHostBuilder.Logging` プロパティを使用して、Blazor WebAssembly アプリでのログ記録を構成します。

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。

ログの構成は、アプリの設定ファイルから読み込むことができます。 詳細については、「<xref:blazor/fundamentals/configuration#logging-configuration>」を参照してください。

## Blazor Server

一般的な ASP.NET Core のログ記録のガイダンスについては、「<xref:fundamentals/logging/index>」を参照してください。

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor WebAssembly SignalR .NET クライアント ログ

<xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入して、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> に渡されたログ プロバイダーに `WebAssemblyConsoleLogger` を追加します。 従来の <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> とは異なり、`WebAssemblyConsoleLogger` はブラウザー固有のログ API (例: `console.log`) のラッパーです。 `WebAssemblyConsoleLogger` を使用すると、ブラウザー コンテキスト内の Mono 内でログ記録できるようになります。

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a>Razor コンポーネントでのログ

ロガーは、アプリのスタートアップ構成を尊重します。

<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の Intellisense 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。

次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILogger> を使用したログ記録を示しています。

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

次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログ記録を示しています。

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

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/index>
