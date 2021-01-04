---
title: ASP.NET Core Blazor のログ
author: guardrex
description: ログ レベルの構成や Razor コンポーネントからログ メッセージを書き込む方法など、Blazor アプリでのログ記録について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 78117fa6e9c7d5aed3fb31bbd3afee55b3b5b875
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506709"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="72791-103">ASP.NET Core Blazor のログ</span><span class="sxs-lookup"><span data-stu-id="72791-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="72791-104"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> プロパティを使用して、Blazor WebAssembly アプリでカスタム ログを構成します。</span><span class="sxs-lookup"><span data-stu-id="72791-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="72791-105">`Program.cs` に <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="72791-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="72791-106">`Program.cs` の `Program.Main` で、<xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> を使用して最小ログ記録レベルを設定し、カスタム ログ プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="72791-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="72791-107">`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="72791-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="72791-108">ログの構成は、アプリの設定ファイルから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="72791-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="72791-109">詳細については、「<xref:blazor/fundamentals/configuration#logging-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72791-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="72791-110">SignalR .NET クライアント ログ</span><span class="sxs-lookup"><span data-stu-id="72791-110">SignalR .NET client logging</span></span>

<span data-ttu-id="72791-111"><xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入して、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> に渡されたログ プロバイダーに `WebAssemblyConsoleLogger` を追加します。</span><span class="sxs-lookup"><span data-stu-id="72791-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="72791-112">従来の <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> とは異なり、`WebAssemblyConsoleLogger` はブラウザー固有のログ API (例: `console.log`) のラッパーです。</span><span class="sxs-lookup"><span data-stu-id="72791-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="72791-113">`WebAssemblyConsoleLogger` を使用すると、ブラウザー コンテキスト内の Mono 内でログ記録できるようになります。</span><span class="sxs-lookup"><span data-stu-id="72791-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

<span data-ttu-id="72791-114"><xref:Microsoft.Extensions.Logging?displayProperty=fullName> の名前空間を追加し、コンポーネントに <xref:Microsoft.Extensions.Logging.ILoggerProvider> を挿入します。</span><span class="sxs-lookup"><span data-stu-id="72791-114">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="72791-115">コンポーネントの [`OnInitializedAsync` メソッド](xref:blazor/components/lifecycle#component-initialization-methods)で、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> を使用します。</span><span class="sxs-lookup"><span data-stu-id="72791-115">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="72791-116">Blazor Server に関連する一般的な ASP.NET Core のログのガイダンスについては、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="72791-116">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="72791-117">Razor コンポーネントでのログ</span><span class="sxs-lookup"><span data-stu-id="72791-117">Log in Razor components</span></span>

<span data-ttu-id="72791-118">ロガーは、アプリのスタートアップ構成を尊重します。</span><span class="sxs-lookup"><span data-stu-id="72791-118">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="72791-119"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の [IntelliSense](/visualstudio/ide/using-intellisense) 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。</span><span class="sxs-lookup"><span data-stu-id="72791-119">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="72791-120">次の例は、コンポーネントで <xref:Microsoft.Extensions.Logging.ILogger> を使用したログを示しています。</span><span class="sxs-lookup"><span data-stu-id="72791-120">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="72791-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="72791-121">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="72791-122">次の例は、コンポーネントで <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログを示しています。</span><span class="sxs-lookup"><span data-stu-id="72791-122">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="72791-123">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="72791-123">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="72791-124">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="72791-124">Additional resources</span></span>

* <xref:fundamentals/logging/index>
