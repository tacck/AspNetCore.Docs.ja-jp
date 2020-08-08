---
title: 2.1 から2.2 または3.0 への移行
author: pakrym
description: 2.1 から2.2 または3.0 を使用する non-ASP.NET Core アプリケーションを移行する方法について説明します。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
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
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015115"
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

## <a name="additional-resources"></a>その他のリソース

* [Microsoft... Logging. コンソール NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。
* <xref:fundamentals/logging/index>
