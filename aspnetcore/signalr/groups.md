---
<span data-ttu-id="c05f8-101">title: ' 作成者でのユーザーとグループの管理 SignalR : 説明: ' ASP.NET Core の SignalR ユーザーとグループの管理の概要。 '</span><span class="sxs-lookup"><span data-stu-id="c05f8-101">title: 'Manage users and groups in SignalR' author: description: 'Overview of ASP.NET Core SignalR User and Group management.'</span></span>
<span data-ttu-id="c05f8-102">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c05f8-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c05f8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c05f8-103">'Blazor'</span></span>
- <span data-ttu-id="c05f8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c05f8-104">'Identity'</span></span>
- <span data-ttu-id="c05f8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c05f8-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c05f8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c05f8-106">'Razor'</span></span>
- <span data-ttu-id="c05f8-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c05f8-107">'SignalR' uid:</span></span> 

---

# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="c05f8-108">でのユーザーとグループの管理SignalR</span><span class="sxs-lookup"><span data-stu-id="c05f8-108">Manage users and groups in SignalR</span></span>

<span data-ttu-id="c05f8-109">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="c05f8-109">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="c05f8-110">特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c05f8-110"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="c05f8-111">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c05f8-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="c05f8-112">のユーザーSignalR</span><span class="sxs-lookup"><span data-stu-id="c05f8-112">Users in SignalR</span></span>

<span data-ttu-id="c05f8-113">の1人のユーザー SignalR がアプリに対して複数の接続を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-113">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="c05f8-114">たとえば、ユーザーは自分のデスクトップや電話に接続することができます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-114">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="c05f8-115">各デバイスには個別の接続があり SignalR ますが、これらはすべて同じユーザーに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="c05f8-115">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="c05f8-116">ユーザーにメッセージが送信されると、そのユーザーに関連付けられているすべての接続がメッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="c05f8-116">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="c05f8-117">接続のユーザー識別子には、ハブのプロパティからアクセスでき `Context.UserIdentifier` ます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-117">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="c05f8-118">既定では、は、 SignalR `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 接続に関連付けられているのをユーザー識別子として使用します。</span><span class="sxs-lookup"><span data-stu-id="c05f8-118">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="c05f8-119">この動作をカスタマイズするには、「[要求を使用して id 処理をカスタマイズ](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c05f8-119">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="c05f8-120">`User`次の例に示すように、ハブメソッドでユーザー識別子を関数に渡すことによって、特定のユーザーにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="c05f8-120">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="c05f8-121">ユーザー識別子では、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-121">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="c05f8-122">のグループSignalR</span><span class="sxs-lookup"><span data-stu-id="c05f8-122">Groups in SignalR</span></span>

<span data-ttu-id="c05f8-123">グループとは、名前に関連付けられている接続のコレクションです。</span><span class="sxs-lookup"><span data-stu-id="c05f8-123">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="c05f8-124">メッセージは、グループ内のすべての接続に送信できます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-124">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="c05f8-125">グループはアプリケーションによって管理されるため、接続または複数の接続に送信する方法としては、グループをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c05f8-125">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="c05f8-126">接続は、複数のグループのメンバーになることができます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-126">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="c05f8-127">グループは、各部屋をグループとして表すことができるチャットアプリケーションのようなものに最適です。</span><span class="sxs-lookup"><span data-stu-id="c05f8-127">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="c05f8-128">接続は、メソッドとメソッドを使用してグループに追加されるか、グループから削除され `AddToGroupAsync` `RemoveFromGroupAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-128">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="c05f8-129">接続を再接続しても、グループのメンバーシップは保持されません。</span><span class="sxs-lookup"><span data-stu-id="c05f8-129">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="c05f8-130">再確立された場合、接続はグループに再度参加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c05f8-130">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="c05f8-131">アプリケーションが複数のサーバーにスケーリングされている場合、この情報は利用できないため、グループのメンバーをカウントすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c05f8-131">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="c05f8-132">グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="c05f8-132">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="c05f8-133">そのグループに対して資格情報が有効な場合にのみ、ユーザーがグループに追加されると、そのグループに送信されたメッセージは、承認されたユーザーのみに送られます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-133">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="c05f8-134">ただし、グループはセキュリティ機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="c05f8-134">However, groups are not a security feature.</span></span> <span data-ttu-id="c05f8-135">認証要求には、有効期限や失効など、グループにはない機能があります。</span><span class="sxs-lookup"><span data-stu-id="c05f8-135">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="c05f8-136">グループにアクセスするためのユーザーのアクセス許可が取り消された場合、アプリはユーザーをグループから明示的に削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c05f8-136">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="c05f8-137">グループ名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="c05f8-137">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c05f8-138">関連リソース</span><span class="sxs-lookup"><span data-stu-id="c05f8-138">Related resources</span></span>

* [<span data-ttu-id="c05f8-139">開始するには</span><span class="sxs-lookup"><span data-stu-id="c05f8-139">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c05f8-140">ハブ</span><span class="sxs-lookup"><span data-stu-id="c05f8-140">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c05f8-141">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="c05f8-141">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
