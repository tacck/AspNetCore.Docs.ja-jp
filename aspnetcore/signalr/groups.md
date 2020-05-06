---
title: でのユーザーとグループの管理SignalR
author: bradygaster
description: ASP.NET Core SignalRユーザーとグループの管理の概要について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776299"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="f35a0-103">でのユーザーとグループの管理SignalR</span><span class="sxs-lookup"><span data-stu-id="f35a0-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="f35a0-104">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="f35a0-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="f35a0-105">特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できるようにします。</span><span class="sxs-lookup"><span data-stu-id="f35a0-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="f35a0-106">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f35a0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="f35a0-107">のユーザーSignalR</span><span class="sxs-lookup"><span data-stu-id="f35a0-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="f35a0-108">特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="f35a0-109">既定ではSignalR 、は`ClaimTypes.NameIdentifier` 、接続`ClaimsPrincipal`に関連付けられているのをユーザー識別子として使用します。</span><span class="sxs-lookup"><span data-stu-id="f35a0-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="f35a0-110">1人のユーザーがSignalRアプリに対して複数の接続を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="f35a0-111">たとえば、ユーザーは自分のデスクトップや電話に接続することができます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="f35a0-112">各デバイスには個別SignalRの接続がありますが、これらはすべて同じユーザーに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="f35a0-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="f35a0-113">ユーザーにメッセージが送信されると、そのユーザーに関連付けられているすべての接続がメッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="f35a0-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="f35a0-114">接続のユーザー識別子には、ハブの`Context.UserIdentifier`プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="f35a0-115">次の例に示すように、ハブメソッドでユーザー識別子を`User`関数に渡すことによって、特定のユーザーにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="f35a0-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="f35a0-116">ユーザー識別子では、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="f35a0-117">のグループSignalR</span><span class="sxs-lookup"><span data-stu-id="f35a0-117">Groups in SignalR</span></span>

<span data-ttu-id="f35a0-118">グループとは、名前に関連付けられている接続のコレクションです。</span><span class="sxs-lookup"><span data-stu-id="f35a0-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="f35a0-119">メッセージは、グループ内のすべての接続に送信できます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="f35a0-120">グループはアプリケーションによって管理されるため、接続または複数の接続に送信する方法としては、グループをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f35a0-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="f35a0-121">接続は、複数のグループのメンバーになることができます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="f35a0-122">これにより、グループはチャットアプリケーションのように理想的であり、各部屋をグループとして表すことができます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="f35a0-123">接続は`AddToGroupAsync` 、メソッドとメソッドを使用して、 `RemoveFromGroupAsync`グループに追加したり、グループから削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="f35a0-124">接続を再接続しても、グループのメンバーシップは保持されません。</span><span class="sxs-lookup"><span data-stu-id="f35a0-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="f35a0-125">再確立された場合、接続はグループに再度参加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f35a0-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="f35a0-126">アプリケーションが複数のサーバーにスケーリングされている場合、この情報は利用できないため、グループのメンバーをカウントすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f35a0-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="f35a0-127">グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="f35a0-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="f35a0-128">そのグループに対して資格情報が有効である場合にのみ、グループにユーザーを追加すると、そのグループに送信されたメッセージは、承認されたユーザーのみに送られます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="f35a0-129">ただし、グループはセキュリティ機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="f35a0-129">However, groups are not a security feature.</span></span> <span data-ttu-id="f35a0-130">認証要求には、有効期限や失効など、グループにはない機能があります。</span><span class="sxs-lookup"><span data-stu-id="f35a0-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="f35a0-131">グループにアクセスするためのユーザーのアクセス許可が取り消された場合は、手動で検出し、グループから削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f35a0-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="f35a0-132">グループ名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="f35a0-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f35a0-133">関連リソース</span><span class="sxs-lookup"><span data-stu-id="f35a0-133">Related resources</span></span>

* [<span data-ttu-id="f35a0-134">開始するには</span><span class="sxs-lookup"><span data-stu-id="f35a0-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f35a0-135">ハブ</span><span class="sxs-lookup"><span data-stu-id="f35a0-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f35a0-136">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="f35a0-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
