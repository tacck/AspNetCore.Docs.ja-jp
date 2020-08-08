---
title: でのユーザーとグループの管理SignalR
author: bradygaster
description: ASP.NET Core ユーザーとグループの管理の概要につい SignalR て説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: 3a1182b793c7d05a11f0b5f16f4d406a9063f023
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021030"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="c5246-103">でのユーザーとグループの管理SignalR</span><span class="sxs-lookup"><span data-stu-id="c5246-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="c5246-104">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="c5246-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="c5246-105">SignalR特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c5246-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="c5246-106">[サンプル コードを表示またはダウンロードします](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(ダウンロード方法)](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="c5246-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="c5246-107">のユーザーSignalR</span><span class="sxs-lookup"><span data-stu-id="c5246-107">Users in SignalR</span></span>

<span data-ttu-id="c5246-108">の1人のユーザー SignalR がアプリに対して複数の接続を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="c5246-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="c5246-109">たとえば、ユーザーは自分のデスクトップや電話に接続することができます。</span><span class="sxs-lookup"><span data-stu-id="c5246-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="c5246-110">各デバイスには個別の接続があり SignalR ますが、これらはすべて同じユーザーに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="c5246-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="c5246-111">ユーザーにメッセージが送信されると、そのユーザーに関連付けられているすべての接続がメッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="c5246-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="c5246-112">接続のユーザー識別子には、ハブのプロパティからアクセスでき `Context.UserIdentifier` ます。</span><span class="sxs-lookup"><span data-stu-id="c5246-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="c5246-113">既定では、は、 SignalR `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 接続に関連付けられているのをユーザー識別子として使用します。</span><span class="sxs-lookup"><span data-stu-id="c5246-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="c5246-114">この動作をカスタマイズするには、「[要求を使用して id 処理をカスタマイズ](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c5246-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="c5246-115">`User`次の例に示すように、ハブメソッドでユーザー識別子を関数に渡すことによって、特定のユーザーにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="c5246-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="c5246-116">ユーザー識別子では、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c5246-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="c5246-117">のグループSignalR</span><span class="sxs-lookup"><span data-stu-id="c5246-117">Groups in SignalR</span></span>

<span data-ttu-id="c5246-118">グループとは、名前に関連付けられている接続のコレクションです。</span><span class="sxs-lookup"><span data-stu-id="c5246-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="c5246-119">メッセージは、グループ内のすべての接続に送信できます。</span><span class="sxs-lookup"><span data-stu-id="c5246-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="c5246-120">グループはアプリケーションによって管理されるため、接続または複数の接続に送信する方法としては、グループをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c5246-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="c5246-121">接続は、複数のグループのメンバーになることができます。</span><span class="sxs-lookup"><span data-stu-id="c5246-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="c5246-122">グループは、各部屋をグループとして表すことができるチャットアプリケーションのようなものに最適です。</span><span class="sxs-lookup"><span data-stu-id="c5246-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="c5246-123">接続は、メソッドとメソッドを使用してグループに追加されるか、グループから削除され `AddToGroupAsync` `RemoveFromGroupAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c5246-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="c5246-124">接続を再接続しても、グループのメンバーシップは保持されません。</span><span class="sxs-lookup"><span data-stu-id="c5246-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="c5246-125">再確立された場合、接続はグループに再度参加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5246-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="c5246-126">アプリケーションが複数のサーバーにスケーリングされている場合、この情報は利用できないため、グループのメンバーをカウントすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c5246-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="c5246-127">グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="c5246-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="c5246-128">そのグループに対して資格情報が有効な場合にのみ、ユーザーがグループに追加されると、そのグループに送信されたメッセージは、承認されたユーザーのみに送られます。</span><span class="sxs-lookup"><span data-stu-id="c5246-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="c5246-129">ただし、グループはセキュリティ機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="c5246-129">However, groups are not a security feature.</span></span> <span data-ttu-id="c5246-130">認証要求には、有効期限や失効など、グループにはない機能があります。</span><span class="sxs-lookup"><span data-stu-id="c5246-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="c5246-131">グループにアクセスするためのユーザーのアクセス許可が取り消された場合、アプリはユーザーをグループから明示的に削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5246-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="c5246-132">グループ名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c5246-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c5246-133">関連リソース</span><span class="sxs-lookup"><span data-stu-id="c5246-133">Related resources</span></span>

* [<span data-ttu-id="c5246-134">開始するには</span><span class="sxs-lookup"><span data-stu-id="c5246-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c5246-135">取って代わり</span><span class="sxs-lookup"><span data-stu-id="c5246-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c5246-136">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="c5246-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
