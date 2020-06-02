---
title: Microsoft. Extensions. Logging 2.1 から2.2 または 3.0 author: pakrym description: non-ASP.NET を使用する Core アプリケーションを2.1 から2.2 または3.0 に移行する方法について説明します。
ms. author: pakrym: mvc ms. date: 01/04/2019 no loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: migration/logging-nonaspnetcore

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>2.1 から2.2 または3.0 への移行

この記事では、 `Microsoft.Extensions.Logging` 2.1 から2.2 または3.0 までを使用する Non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。

## <a name="21-to-22"></a>2.1 から 2.2

を手動で作成 `ServiceCollection` して、を呼び出し `AddLogging` ます。

2.1 の例:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2 の例:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 ~ 3.0

3.0 では、を使用 `LoggingFactory.Create` します。

2.1 の例:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 の例:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>その他の技術情報

* [Microsoft... Logging. コンソール NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。
* <xref:fundamentals/logging/index>
