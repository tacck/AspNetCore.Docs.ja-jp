---
title: AuthorizationMiddleware の動作をカスタマイズする
author: pranavkm
ms.author: prkrishn
description: この記事では、AuthorizationMiddleware の結果処理をカスタマイズする方法について説明します。
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156768"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="e2f12-103">AuthorizationMiddleware の動作をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="e2f12-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="e2f12-104">アプリケーションはを登録して、 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` ミドルウェアが承認結果を処理する方法をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="e2f12-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="e2f12-105">アプリケーションでは、カスタマイズされたミドルウェアを使用して次のことができます。</span><span class="sxs-lookup"><span data-stu-id="e2f12-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="e2f12-106">カスタマイズされた応答を返します。</span><span class="sxs-lookup"><span data-stu-id="e2f12-106">Return customized responses.</span></span>
* <span data-ttu-id="e2f12-107">既定のチャレンジまたは禁止応答を強化します。</span><span class="sxs-lookup"><span data-stu-id="e2f12-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="e2f12-108">次のコードは、特定の種類の承認エラーに対するカスタム応答を返す承認ハンドラーの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="e2f12-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="e2f12-109">登録 `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e2f12-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->