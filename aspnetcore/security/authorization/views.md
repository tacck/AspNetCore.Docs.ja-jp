---
title: ASP.NET Core MVC でのビューベースの承認
author: rick-anderson
description: このドキュメントでは、ASP.NET Core ビュー内で承認サービスを挿入および利用する方法について説明し Razor ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
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
uid: security/authorization/views
ms.openlocfilehash: 775ebdffe2b0753de18bf07d9ff1193235a45b17
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629887"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC でのビューベースの承認

多くの場合、開発者は、現在のユーザー id に基づいて UI を表示、非表示にする、または変更する必要があります。 MVC ビュー内の承認サービスには、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってアクセスできます。 認証サービスをビューに挿入するには Razor 、ディレクティブを使用し `@inject` ます。

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

すべてのビューで承認サービスを使用する場合は、 `@inject` ディレクティブを*Views*ディレクトリの *_ViewImports*ファイルに配置します。 詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。

挿入された承認サービスを使用して、 `AuthorizeAsync` [リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法でを呼び出します。

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

場合によっては、リソースがビューモデルになります。 `AuthorizeAsync`[リソースベースの承認](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)時に確認するのとまったく同じ方法で呼び出すことができます。

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

前のコードでは、モデルはポリシーの評価で考慮する必要があるリソースとして渡されます。

> [!WARNING]
> 唯一の承認チェックとして、アプリの UI 要素の表示の切り替えに依存しないでください。 UI 要素を非表示にしても、関連付けられているコントローラーアクションへのアクセスを完全に防ぐことはできません。 たとえば、前のコードスニペットのボタンについて考えてみます。 ユーザー `Edit` は、相対リソース URL が */Document/Edit/1*であることを知っていれば、アクションメソッドを呼び出すことができます。 このため、 `Edit` アクションメソッドは独自の承認チェックを実行する必要があります。
