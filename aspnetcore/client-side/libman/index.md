---
title: LibMan を使用した ASP.NET Core でのクライアント側ライブラリの取得
author: scottaddie
description: ライブラリ マネージャー (LibMan) を使用して、ASP.NET Core プロジェクトにクライアント側ライブラリの資産をインストールする方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 62b6859b0a8ad0f98a2684f21c0f68dbbd67c4c1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054659"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="bea15-103">LibMan を使用した ASP.NET Core でのクライアント側ライブラリの取得</span><span class="sxs-lookup"><span data-stu-id="bea15-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="bea15-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="bea15-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="bea15-105">ライブラリ マネージャー (LibMan) は、軽量なクライアント側ライブラリ取得ツールです。</span><span class="sxs-lookup"><span data-stu-id="bea15-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="bea15-106">LibMan は、人気のあるライブラリとフレームワークをファイル システムまたは[コンテンツ配信ネットワーク (CDN)](https://wikipedia.org/wiki/Content_delivery_network) からダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="bea15-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="bea15-107">サポートされる CDN には、[CDNJS](https://cdnjs.com/)、[jsDelivr](https://www.jsdelivr.com/)、および [unpkg](https://unpkg.com/#/) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="bea15-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="bea15-108">選択したライブラリ ファイルが取り込まれ、ASP.NET Core プロジェクト内の適切な場所に配置されます。</span><span class="sxs-lookup"><span data-stu-id="bea15-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="bea15-109">LibMan のユース ケース</span><span class="sxs-lookup"><span data-stu-id="bea15-109">LibMan use cases</span></span>

<span data-ttu-id="bea15-110">LibMan には次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="bea15-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="bea15-111">必要なライブラリ ファイルのみがダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="bea15-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="bea15-112">[Node.js](https://nodejs.org)、[npm](https://www.npmjs.com)、[WebPack](https://webpack.js.org) などの追加ツールは、ライブラリ内のファイルのサブセットを取得するためには必要ありません。</span><span class="sxs-lookup"><span data-stu-id="bea15-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="bea15-113">ビルド タスクや手動でのファイル コピーを実行しなくても、ファイルを特定の場所に置くことができます。</span><span class="sxs-lookup"><span data-stu-id="bea15-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="bea15-114">LibMan の利点について詳しくは、「[Modern front-end web development in Visual Studio 2017: LibMan segment (Visual Studio 2017 での最新のフロントエンド Web 開発: LibMan セグメント)」](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bea15-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="bea15-115">LibMan はパッケージ管理システムではありません。</span><span class="sxs-lookup"><span data-stu-id="bea15-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="bea15-116">npm や [yarn](https://yarnpkg.com) などのパッケージ マネージャーを既に使用している場合は、引き続き使用してください。</span><span class="sxs-lookup"><span data-stu-id="bea15-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="bea15-117">LibMan は、このようなツールの置き換えとして開発されたものではありません。</span><span class="sxs-lookup"><span data-stu-id="bea15-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bea15-118">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bea15-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="bea15-119">LibMan の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="bea15-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
