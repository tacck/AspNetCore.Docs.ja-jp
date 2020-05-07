---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771117"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="3c2d2-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="3c2d2-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="3c2d2-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3c2d2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="3c2d2-105">ブラウザー要件</span><span class="sxs-lookup"><span data-stu-id="3c2d2-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="3c2d2-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3c2d2-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="3c2d2-107">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="3c2d2-107">Browser</span></span>                          | <span data-ttu-id="3c2d2-108">バージョン</span><span class="sxs-lookup"><span data-stu-id="3c2d2-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="3c2d2-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="3c2d2-109">Microsoft Edge</span></span>                   | <span data-ttu-id="3c2d2-110">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-110">Current</span></span>               |
| <span data-ttu-id="3c2d2-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="3c2d2-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="3c2d2-112">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-112">Current</span></span>               |
| <span data-ttu-id="3c2d2-113">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="3c2d2-113">Google Chrome, including Android</span></span> | <span data-ttu-id="3c2d2-114">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-114">Current</span></span>               |
| <span data-ttu-id="3c2d2-115">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="3c2d2-115">Safari, including iOS</span></span>            | <span data-ttu-id="3c2d2-116">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-116">Current</span></span>               |
| <span data-ttu-id="3c2d2-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="3c2d2-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="3c2d2-118">サポートされていません&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2d2-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="3c2d2-119">&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="3c2d2-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="3c2d2-120">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="3c2d2-120">Blazor Server</span></span>

| <span data-ttu-id="3c2d2-121">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="3c2d2-121">Browser</span></span>                          | <span data-ttu-id="3c2d2-122">バージョン</span><span class="sxs-lookup"><span data-stu-id="3c2d2-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="3c2d2-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="3c2d2-123">Microsoft Edge</span></span>                   | <span data-ttu-id="3c2d2-124">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-124">Current</span></span>    |
| <span data-ttu-id="3c2d2-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="3c2d2-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="3c2d2-126">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-126">Current</span></span>    |
| <span data-ttu-id="3c2d2-127">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="3c2d2-127">Google Chrome, including Android</span></span> | <span data-ttu-id="3c2d2-128">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-128">Current</span></span>    |
| <span data-ttu-id="3c2d2-129">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="3c2d2-129">Safari, including iOS</span></span>            | <span data-ttu-id="3c2d2-130">[現在]</span><span class="sxs-lookup"><span data-stu-id="3c2d2-130">Current</span></span>    |
| <span data-ttu-id="3c2d2-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="3c2d2-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="3c2d2-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="3c2d2-132">11&dagger;</span></span> |

<span data-ttu-id="3c2d2-133">&dagger;追加のポリフィルが必要です (たとえば、[Polyfill.io](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。</span><span class="sxs-lookup"><span data-stu-id="3c2d2-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c2d2-134">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3c2d2-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
