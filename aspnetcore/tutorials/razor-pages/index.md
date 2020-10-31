---
title: 'チュートリアル: ASP.NET Core で :::no-loc(Razor)::: Pages Web アプリを作成する'
author: rick-anderson
description: 'Visual Studio、ASP.NET Core、EF Core を使用して Windows で :::no-loc(Razor)::: Pages Web アプリを作成します。'
ms.author: riande
ms.date: 08/09/2019
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
uid: tutorials/razor-pages/index
ms.openlocfilehash: 0feb60c7e0189bf12d584c3916e0254b3be729c0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058065"
---
# <a name="tutorial-create-a-no-locrazor-pages-web-app-with-aspnet-core"></a><span data-ttu-id="06ef1-103">チュートリアル: ASP.NET Core で :::no-loc(Razor)::: Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="06ef1-103">Tutorial: Create a :::no-loc(Razor)::: Pages web app with ASP.NET Core</span></span>

<span data-ttu-id="06ef1-104">このチュートリアルのシリーズでは、:::no-loc(Razor)::: Pages Web アプリの作成の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="06ef1-104">This series of tutorials explains the basics of building a :::no-loc(Razor)::: Pages web app.</span></span> 

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="06ef1-105">このシリーズには以下のチュートリアルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="06ef1-105">This series includes the following tutorials:</span></span>

1. [<span data-ttu-id="06ef1-106">:::no-loc(Razor)::: Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="06ef1-106">Create a :::no-loc(Razor)::: Pages web app</span></span>](xref:tutorials/razor-pages/razor-pages-start)
1. [<span data-ttu-id="06ef1-107">:::no-loc(Razor)::: Pages アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="06ef1-107">Add a model to a :::no-loc(Razor)::: Pages app</span></span>](xref:tutorials/razor-pages/model)
1. [<span data-ttu-id="06ef1-108">:::no-loc(Razor)::: ページのスキャフォールディング (生成)</span><span class="sxs-lookup"><span data-stu-id="06ef1-108">Scaffold (generate) :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/page)
1. [<span data-ttu-id="06ef1-109">データベースの使用</span><span class="sxs-lookup"><span data-stu-id="06ef1-109">Work with a database</span></span>](xref:tutorials/razor-pages/sql)
1. [<span data-ttu-id="06ef1-110">:::no-loc(Razor)::: ページの更新</span><span class="sxs-lookup"><span data-stu-id="06ef1-110">Update :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/da1)
1. [<span data-ttu-id="06ef1-111">検索の追加</span><span class="sxs-lookup"><span data-stu-id="06ef1-111">Add search</span></span>](xref:tutorials/razor-pages/search)
1. [<span data-ttu-id="06ef1-112">新しいフィールドの追加</span><span class="sxs-lookup"><span data-stu-id="06ef1-112">Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
1. [<span data-ttu-id="06ef1-113">検証の追加</span><span class="sxs-lookup"><span data-stu-id="06ef1-113">Add validation</span></span>](xref:tutorials/razor-pages/validation)

<span data-ttu-id="06ef1-114">最後には、映画のデータベースを表示して管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="06ef1-114">At the end, you'll have an app that can display and manage a database of movies.</span></span>

![サンプル アプリのサンプル ページ](index/_static/sample-page.png)
