---
title: 'ASP.NET Core :::no-loc(SignalR)::: でサポートされているプラットフォーム'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: でサポートされているプラットフォームについて学習します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051019"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="14f75-103">ASP.NET Core :::no-loc(SignalR)::: でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="14f75-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="14f75-104">サーバー システムの要件</span><span class="sxs-lookup"><span data-stu-id="14f75-104">Server system requirements</span></span>

<span data-ttu-id="14f75-105">:::no-loc(SignalR)::: ASP.NET Core は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。</span><span class="sxs-lookup"><span data-stu-id="14f75-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="14f75-106">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="14f75-106">JavaScript client</span></span>

<span data-ttu-id="14f75-107">[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。</span><span class="sxs-lookup"><span data-stu-id="14f75-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="14f75-108">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="14f75-108">Browser</span></span>                          | <span data-ttu-id="14f75-109">バージョン</span><span class="sxs-lookup"><span data-stu-id="14f75-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="14f75-110">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="14f75-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="14f75-111">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="14f75-111">Current&dagger;</span></span> |
| <span data-ttu-id="14f75-112">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="14f75-112">Google Chrome, including Android</span></span> | <span data-ttu-id="14f75-113">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="14f75-113">Current&dagger;</span></span> |
| <span data-ttu-id="14f75-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="14f75-114">Microsoft Edge</span></span>                   | <span data-ttu-id="14f75-115">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="14f75-115">Current&dagger;</span></span> |
| <span data-ttu-id="14f75-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="14f75-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="14f75-117">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="14f75-117">Current&dagger;</span></span> |

<span data-ttu-id="14f75-118">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="14f75-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="14f75-119">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="14f75-119">.NET client</span></span>

<span data-ttu-id="14f75-120">[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="14f75-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="14f75-121">たとえば、xamarin[開発者はを :::no-loc(SignalR)::: 使用](https://github.com/aspnet/Announcements/issues/305)して、xamarin 8.4.0.1 以降と ios アプリを使用して、xamarin 11.14.0.4 以降を使用して android アプリをビルドできます。</span><span class="sxs-lookup"><span data-stu-id="14f75-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="14f75-122">サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="14f75-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="14f75-123">その他のトランスポートはすべてのプラットフォームでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="14f75-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="14f75-124">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="14f75-124">Java client</span></span>

<span data-ttu-id="14f75-125">[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="14f75-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="14f75-126">サポートされていないクライアント</span><span class="sxs-lookup"><span data-stu-id="14f75-126">Unsupported clients</span></span>

<span data-ttu-id="14f75-127">次のクライアントは使用できますが、試験的または非公式です。</span><span class="sxs-lookup"><span data-stu-id="14f75-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="14f75-128">現時点ではサポートされておらず、そうでない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="14f75-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="14f75-129">[C++ クライアント](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="14f75-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="14f75-130">[Swift クライアント](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="14f75-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
