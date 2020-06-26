---
title: ASP.NET Core SignalR でサポートされているプラットフォーム
author: bradygaster
description: ASP.NET Core SignalR でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9d7dd946ae5cf83b76a83ba1faeed0bb3fc31cef
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405823"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR でサポートされているプラットフォーム

## <a name="server-system-requirements"></a>サーバー システムの要件

SignalRASP.NET Core は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。

## <a name="javascript-client"></a>JavaScript クライアント

[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。

| ブラウザー                         | バージョン         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | [現在]&dagger; |
| Mozilla Firefox                 | [現在]&dagger; |
| Google Chrome。Android を含む | [現在]&dagger; |
| SafariiOS を含む            | [現在]&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*Current*は、ブラウザーの最新バージョンを参照します。

## <a name="net-client"></a>.NET クライアント

[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。 たとえば、xamarin[開発者はを SignalR 使用](https://github.com/aspnet/Announcements/issues/305)して、xamarin 8.4.0.1 以降と ios アプリを使用して、xamarin 11.14.0.4 以降を使用して android アプリをビルドできます。

サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。 その他のトランスポートはすべてのプラットフォームでサポートされています。

## <a name="java-client"></a>Java クライアント

[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。

## <a name="unsupported-clients"></a>サポートされていないクライアント

次のクライアントは使用できますが、試験的または非公式です。 現時点ではサポートされておらず、そうでない場合もあります。

* [C++ クライアント](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)
