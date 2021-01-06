---
title: ASP.NET Core Blazor 用のトリマーを構成する
author: guardrex
description: Blazor アプリをビルドする際に中間言語 (IL) リンカー (トリマー) を制御する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055803"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="7e44f-103">ASP.NET Core Blazor 用のトリマーを構成する</span><span class="sxs-lookup"><span data-stu-id="7e44f-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="7e44f-104">作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="7e44f-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="7e44f-105">Blazor WebAssembly では、発行された出力のサイズを縮小するために、[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) トリミングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7e44f-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="7e44f-106">アプリをトリミングするとサイズが最適化されますが、悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7e44f-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="7e44f-107">リフレクションや関連する動的機能を使用するアプリは、トリミングされると中断する可能性があります。トリマーが、動的な動作を認識せず、通常、実行時にリフレクションで必要とされる型を特定できないからです。</span><span class="sxs-lookup"><span data-stu-id="7e44f-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="7e44f-108">そのようなアプリをトリミングするには、コードのリフレクション、およびアプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、トリマーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e44f-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="7e44f-109">トリミングされたアプリが展開後に正しく動作するには、発行された出力を、開発中に頻繁にテストすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="7e44f-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="7e44f-110">.NET アプリのトリミングを無効にするには、アプリのプロジェクト ファイルで、`PublishTrimmed` MSBuild プロパティを `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7e44f-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="7e44f-111">トリマーを構成するためのその他のオプションについては、[トリミングのオプション](/dotnet/core/deploying/trimming-options)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e44f-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e44f-112">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7e44f-112">Additional resources</span></span>

* [<span data-ttu-id="7e44f-113">自己完結型の展開と実行可能ファイルのトリミング</span><span class="sxs-lookup"><span data-stu-id="7e44f-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
