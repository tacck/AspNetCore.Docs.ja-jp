---
title: ASP.NET Core での承認の概要
author: rick-anderson
description: 承認の基本と ASP.NET Core アプリでの承認のしくみについて説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634437"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="6c5cb-103">ASP.NET Core での承認の概要</span><span class="sxs-lookup"><span data-stu-id="6c5cb-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="6c5cb-104">承認とは、ユーザーが実行できる操作を決定するプロセスを指します。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="6c5cb-105">たとえば、管理ユーザーは、ドキュメントライブラリの作成、ドキュメントの追加、ドキュメントの編集、および削除を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="6c5cb-106">ライブラリを操作する管理者以外のユーザーは、ドキュメントの読み取りのみが許可されています。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="6c5cb-107">認証は直交しており、認証から独立しています。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="6c5cb-108">ただし、承認には認証メカニズムが必要です。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="6c5cb-109">認証は、ユーザーを突き止めるするプロセスです。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="6c5cb-110">承認では、現在のユーザーのために 1 つ以上の ID を作成する場合があります。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="6c5cb-111">ASP.NET Core での認証の詳細については、「」を参照してください <xref:security/authentication/index> 。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="6c5cb-112">承認の種類</span><span class="sxs-lookup"><span data-stu-id="6c5cb-112">Authorization types</span></span>

<span data-ttu-id="6c5cb-113">ASP.NET Core 承認は、単純な宣言型の [役割](xref:security/authorization/roles) と豊富な [ポリシーベース](xref:security/authorization/policies) のモデルを提供します。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="6c5cb-114">承認は要件で表現され、ハンドラーは要件に対してユーザーの要求を評価します。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="6c5cb-115">命令型チェックは、ユーザーがアクセスしようとしているリソースのユーザー id とプロパティの両方を評価する単純なポリシーまたはポリシーに基づいて行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="6c5cb-116">名前空間</span><span class="sxs-lookup"><span data-stu-id="6c5cb-116">Namespaces</span></span>

<span data-ttu-id="6c5cb-117">属性と属性を含む承認コンポーネント `AuthorizeAttribute` `AllowAnonymousAttribute` は、 `Microsoft.AspNetCore.Authorization` 名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="6c5cb-118">[単純な承認](xref:security/authorization/simple)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c5cb-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
