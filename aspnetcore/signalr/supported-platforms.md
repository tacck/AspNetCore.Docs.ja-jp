---
title: サポートSignalRされているプラットフォームの ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalRでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772606"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="c8e77-103">SignalR でサポートされているプラットフォームの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8e77-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="c8e77-104">サーバー システムの要件</span><span class="sxs-lookup"><span data-stu-id="c8e77-104">Server system requirements</span></span>

<span data-ttu-id="c8e77-105">ASP.NET Core の SignalR は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="c8e77-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="c8e77-106">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="c8e77-106">JavaScript client</span></span>

<span data-ttu-id="c8e77-107">[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8e77-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="c8e77-108">Browser</span><span class="sxs-lookup"><span data-stu-id="c8e77-108">Browser</span></span>                         | <span data-ttu-id="c8e77-109">Version</span><span class="sxs-lookup"><span data-stu-id="c8e77-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="c8e77-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c8e77-110">Microsoft Edge</span></span>                  | <span data-ttu-id="c8e77-111">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8e77-111">Current&dagger;</span></span> |
| <span data-ttu-id="c8e77-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c8e77-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="c8e77-113">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8e77-113">Current&dagger;</span></span> |
| <span data-ttu-id="c8e77-114">Google Chrome。Android を含む</span><span class="sxs-lookup"><span data-stu-id="c8e77-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="c8e77-115">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8e77-115">Current&dagger;</span></span> |
| <span data-ttu-id="c8e77-116">SafariiOS を含む</span><span class="sxs-lookup"><span data-stu-id="c8e77-116">Safari; includes iOS</span></span>            | <span data-ttu-id="c8e77-117">現在の&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8e77-117">Current&dagger;</span></span> |
| <span data-ttu-id="c8e77-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c8e77-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="c8e77-119">11</span><span class="sxs-lookup"><span data-stu-id="c8e77-119">11</span></span>              |

<span data-ttu-id="c8e77-120">&dagger;*Current*は、ブラウザーの最新バージョンを参照します。</span><span class="sxs-lookup"><span data-stu-id="c8e77-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="c8e77-121">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="c8e77-121">.NET client</span></span>

<span data-ttu-id="c8e77-122">[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8e77-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="c8e77-123">たとえば、xamarin[開発者はをSignalR使用](https://github.com/aspnet/Announcements/issues/305)して、xamarin 8.4.0.1 以降と ios アプリを使用して、xamarin 11.14.0.4 以降を使用して android アプリをビルドできます。</span><span class="sxs-lookup"><span data-stu-id="c8e77-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="c8e77-124">サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="c8e77-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="c8e77-125">その他のトランスポートはすべてのプラットフォームでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8e77-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="c8e77-126">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="c8e77-126">Java client</span></span>

<span data-ttu-id="c8e77-127">[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="c8e77-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="c8e77-128">サポートされていないクライアント</span><span class="sxs-lookup"><span data-stu-id="c8e77-128">Unsupported clients</span></span>

<span data-ttu-id="c8e77-129">次のクライアントは使用できますが、試験的または非公式です。</span><span class="sxs-lookup"><span data-stu-id="c8e77-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="c8e77-130">現時点ではサポートされておらず、そうでない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="c8e77-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="c8e77-131">[C++ クライアント](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="c8e77-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="c8e77-132">[Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="c8e77-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
