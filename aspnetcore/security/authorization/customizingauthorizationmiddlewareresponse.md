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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>AuthorizationMiddleware の動作をカスタマイズする

アプリケーションはを登録して、 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` ミドルウェアが承認結果を処理する方法をカスタマイズできます。 アプリケーションでは、カスタマイズされたミドルウェアを使用して次のことができます。

* カスタマイズされた応答を返します。
* 既定のチャレンジまたは禁止応答を強化します。

次のコードは、特定の種類の承認エラーに対するカスタム応答を返す承認ハンドラーの例を示しています。

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

登録 `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->