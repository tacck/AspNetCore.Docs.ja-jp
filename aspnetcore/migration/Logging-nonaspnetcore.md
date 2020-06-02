---
<span data-ttu-id="247ec-101">title: Microsoft. Extensions. Logging 2.1 から2.2 または 3.0 author: pakrym description: non-ASP.NET を使用する Core アプリケーションを2.1 から2.2 または3.0 に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="247ec-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="247ec-102">ms. author: pakrym: mvc ms. date: 01/04/2019 no loc:</span><span class="sxs-lookup"><span data-stu-id="247ec-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="247ec-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="247ec-103">'Blazor'</span></span>
- <span data-ttu-id="247ec-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="247ec-104">'Identity'</span></span>
- <span data-ttu-id="247ec-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="247ec-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="247ec-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="247ec-106">'Razor'</span></span>
- <span data-ttu-id="247ec-107">' SignalR ' uid: migration/logging-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="247ec-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="247ec-108">2.1 から2.2 または3.0 への移行</span><span class="sxs-lookup"><span data-stu-id="247ec-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="247ec-109">この記事では、 `Microsoft.Extensions.Logging` 2.1 から2.2 または3.0 までを使用する Non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="247ec-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="247ec-110">2.1 から 2.2</span><span class="sxs-lookup"><span data-stu-id="247ec-110">2.1 to 2.2</span></span>

<span data-ttu-id="247ec-111">を手動で作成 `ServiceCollection` して、を呼び出し `AddLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="247ec-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="247ec-112">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="247ec-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="247ec-113">2.2 の例:</span><span class="sxs-lookup"><span data-stu-id="247ec-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="247ec-114">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="247ec-114">2.1 to 3.0</span></span>

<span data-ttu-id="247ec-115">3.0 では、を使用 `LoggingFactory.Create` します。</span><span class="sxs-lookup"><span data-stu-id="247ec-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="247ec-116">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="247ec-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="247ec-117">3.0 の例:</span><span class="sxs-lookup"><span data-stu-id="247ec-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="247ec-118">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="247ec-118">Additional resources</span></span>

* <span data-ttu-id="247ec-119">[Microsoft... Logging. コンソール NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="247ec-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
