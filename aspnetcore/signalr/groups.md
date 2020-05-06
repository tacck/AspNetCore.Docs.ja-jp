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
# <a name="manage-users-and-groups-in-signalr"></a>でのユーザーとグループの管理SignalR

[Brennan Conroy](https://github.com/BrennanConroy)

SignalR特定のユーザーに関連付けられているすべての接続、および名前付きの接続グループにメッセージを送信できるようにします。

[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>のユーザーSignalR

SignalR特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。 既定ではSignalR 、は`ClaimTypes.NameIdentifier` 、接続`ClaimsPrincipal`に関連付けられているのをユーザー識別子として使用します。 1人のユーザーがSignalRアプリに対して複数の接続を持つことができます。 たとえば、ユーザーは自分のデスクトップや電話に接続することができます。 各デバイスには個別SignalRの接続がありますが、これらはすべて同じユーザーに関連付けられています。 ユーザーにメッセージが送信されると、そのユーザーに関連付けられているすべての接続がメッセージを受信します。 接続のユーザー識別子には、ハブの`Context.UserIdentifier`プロパティからアクセスできます。

次の例に示すように、ハブメソッドでユーザー識別子を`User`関数に渡すことによって、特定のユーザーにメッセージを送信します。

> [!NOTE]
> ユーザー識別子では、大文字と小文字が区別されます。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>のグループSignalR

グループとは、名前に関連付けられている接続のコレクションです。 メッセージは、グループ内のすべての接続に送信できます。 グループはアプリケーションによって管理されるため、接続または複数の接続に送信する方法としては、グループをお勧めします。 接続は、複数のグループのメンバーになることができます。 これにより、グループはチャットアプリケーションのように理想的であり、各部屋をグループとして表すことができます。 接続は`AddToGroupAsync` 、メソッドとメソッドを使用して、 `RemoveFromGroupAsync`グループに追加したり、グループから削除したりできます。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

接続を再接続しても、グループのメンバーシップは保持されません。 再確立された場合、接続はグループに再度参加する必要があります。 アプリケーションが複数のサーバーにスケーリングされている場合、この情報は利用できないため、グループのメンバーをカウントすることはできません。

グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core で[認証と承認](xref:signalr/authn-and-authz)の機能を使用します。 そのグループに対して資格情報が有効である場合にのみ、グループにユーザーを追加すると、そのグループに送信されたメッセージは、承認されたユーザーのみに送られます。 ただし、グループはセキュリティ機能ではありません。 認証要求には、有効期限や失効など、グループにはない機能があります。 グループにアクセスするためのユーザーのアクセス許可が取り消された場合は、手動で検出し、グループから削除する必要があります。

> [!NOTE]
> グループ名は大文字と小文字が区別されます。

## <a name="related-resources"></a>関連リソース

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
