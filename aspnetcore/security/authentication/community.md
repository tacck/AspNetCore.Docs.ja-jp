---
title: ASP.NET Core のコミュニティ OSS 認証オプション
author: rick-anderson
description: ASP.NET Core のオープンソースの認証オプションを検出します。
ms.author: riande
ms.date: 10/28/2019
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
uid: security/authentication/community
ms.openlocfilehash: fcea5384b9f1d9f7e4c5a210dd2ceb7cf2e957ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061366"
---
# <a name="community-oss-authentication-options-for-aspnet-core"></a><span data-ttu-id="50173-103">ASP.NET Core のコミュニティ OSS 認証オプション</span><span class="sxs-lookup"><span data-stu-id="50173-103">Community OSS authentication options for ASP.NET Core</span></span>

<span data-ttu-id="50173-104">このページには、コミュニティによって提供される、ASP.NET Core 用のオープンソースの認証オプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="50173-104">This page contains community-provided, open source authentication options for ASP.NET Core.</span></span> <span data-ttu-id="50173-105">このページは、新しいプロバイダが使用可能になると定期的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="50173-105">This page is periodically updated as new providers become available.</span></span>

## <a name="oss-authentication-providers"></a><span data-ttu-id="50173-106">OSS 認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="50173-106">OSS authentication providers</span></span>

<span data-ttu-id="50173-107">次の一覧はアルファベット順に並べ替えられています。</span><span class="sxs-lookup"><span data-stu-id="50173-107">The list below is sorted alphabetically.</span></span>

| <span data-ttu-id="50173-108">Name</span><span class="sxs-lookup"><span data-stu-id="50173-108">Name</span></span> | <span data-ttu-id="50173-109">[説明]</span><span class="sxs-lookup"><span data-stu-id="50173-109">Description</span></span> |
| ---- | ----------- |
| [<span data-ttu-id="50173-110">AspNet. Security. OpenIdConnect. サーバー (ASOS)</span><span class="sxs-lookup"><span data-stu-id="50173-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span></span>](https://github.com/aspnet-contrib/AspNet.Security.OpenIdConnect.Server) | <span data-ttu-id="50173-111">ASOS は、ASP.NET Core および OWIN/Katana 用の低レベルのプロトコル優先の OpenID Connect サーバーフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="50173-111">ASOS is a low-level, protocol-first OpenID Connect server framework for ASP.NET Core and OWIN/Katana.</span></span> |
| [<span data-ttu-id="50173-112">Gluu サーバー</span><span class="sxs-lookup"><span data-stu-id="50173-112">Gluu Server</span></span>](https://gluu.org/) | <span data-ttu-id="50173-113">Id、アクセス管理 (IAM)、シングルサインオン (SSO) 用のエンタープライズ対応のオープンソースソフトウェア。</span><span class="sxs-lookup"><span data-stu-id="50173-113">Enterprise ready, open source software for identity, access management (IAM), and single sign-on (SSO).</span></span> <span data-ttu-id="50173-114">詳細については、 [Gluu の製品ドキュメント](https://gluu.org/docs/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="50173-114">For more information, see the [Gluu Product Documentation](https://gluu.org/docs/).</span></span> |
| [<span data-ttu-id="50173-115">:::no-loc(Identity):::サーバー</span><span class="sxs-lookup"><span data-stu-id="50173-115">:::no-loc(Identity):::Server</span></span>](https://identityserver.io/) | <span data-ttu-id="50173-116">:::no-loc(Identity):::サーバーは ASP.NET Core 用の OpenID Connect および OAuth 2.0 フレームワークであり、OpenID Foundation および .NET Foundation のガバナンスによって正式に認定されています。</span><span class="sxs-lookup"><span data-stu-id="50173-116">:::no-loc(Identity):::Server is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core, officially certified by the OpenID Foundation and under governance of the .NET Foundation.</span></span> <span data-ttu-id="50173-117">詳細については、「 [Welcome to :::no-loc(Identity)::: サーバー 4 (ドキュメント)](https://identityserver4.readthedocs.io/en/latest/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="50173-117">For more information, see [Welcome to :::no-loc(Identity):::Server4 (Documentation)](https://identityserver4.readthedocs.io/en/latest/).</span></span> |
| [<span data-ttu-id="50173-118">OpenIddict</span><span class="sxs-lookup"><span data-stu-id="50173-118">OpenIddict</span></span>](https://github.com/openiddict/openiddict-core) | <span data-ttu-id="50173-119">OpenIddict は、ASP.NET Core 用の使いやすい OpenID Connect サーバーです。</span><span class="sxs-lookup"><span data-stu-id="50173-119">OpenIddict is an easy-to-use OpenID Connect server for ASP.NET Core.</span></span> |

<span data-ttu-id="50173-120">プロバイダーを追加するには、 [このページを編集](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md)します。</span><span class="sxs-lookup"><span data-stu-id="50173-120">To add a provider, [edit this page](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span></span>
