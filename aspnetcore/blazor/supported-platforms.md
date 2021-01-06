---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054958"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="007f2-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="007f2-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="007f2-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="007f2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="007f2-105">Blazor WebAssembly と Blazor Server は、次の表に示すブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="007f2-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="007f2-106">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="007f2-106">Browser</span></span>                          | <span data-ttu-id="007f2-107">バージョン</span><span class="sxs-lookup"><span data-stu-id="007f2-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="007f2-108">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="007f2-109">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-109">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-110">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-110">Google Chrome, including Android</span></span> | <span data-ttu-id="007f2-111">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-111">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="007f2-112">Microsoft Edge</span></span>                   | <span data-ttu-id="007f2-113">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-113">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="007f2-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="007f2-115">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-115">Current&dagger;</span></span> |  

<span data-ttu-id="007f2-116">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="007f2-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="007f2-117">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="007f2-117">Browser</span></span>                          | <span data-ttu-id="007f2-118">バージョン</span><span class="sxs-lookup"><span data-stu-id="007f2-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="007f2-119">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="007f2-120">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-120">Current&dagger;</span></span>       |
| <span data-ttu-id="007f2-121">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-121">Google Chrome, including Android</span></span> | <span data-ttu-id="007f2-122">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-122">Current&dagger;</span></span>       |
| <span data-ttu-id="007f2-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="007f2-123">Microsoft Edge</span></span>                   | <span data-ttu-id="007f2-124">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-124">Current&dagger;</span></span>       |
| <span data-ttu-id="007f2-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="007f2-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="007f2-126">サポートされていません&Dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="007f2-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="007f2-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="007f2-128">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-128">Current&dagger;</span></span>       |  

<span data-ttu-id="007f2-129">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="007f2-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="007f2-130">&Dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="007f2-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="007f2-131">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="007f2-131">Browser</span></span>                          | <span data-ttu-id="007f2-132">バージョン</span><span class="sxs-lookup"><span data-stu-id="007f2-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="007f2-133">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="007f2-134">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-134">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-135">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="007f2-135">Google Chrome, including Android</span></span> | <span data-ttu-id="007f2-136">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-136">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="007f2-137">Microsoft Edge</span></span>                   | <span data-ttu-id="007f2-138">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-138">Current&dagger;</span></span> |
| <span data-ttu-id="007f2-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="007f2-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="007f2-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-140">11&Dagger;</span></span>      |
| <span data-ttu-id="007f2-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="007f2-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="007f2-142">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="007f2-142">Current&dagger;</span></span> |

<span data-ttu-id="007f2-143">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="007f2-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="007f2-144">&Dagger;追加のポリフィルが必要です。</span><span class="sxs-lookup"><span data-stu-id="007f2-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="007f2-145">たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます</span><span class="sxs-lookup"><span data-stu-id="007f2-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="007f2-146">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="007f2-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
