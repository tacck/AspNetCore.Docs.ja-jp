---
title: ASP.NET Core Security の概要
author: rick-anderson
description: ASP.NET Core での認証、承認、およびセキュリティの基本を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: 2da3585df16cbec4b87db32cee1bb9138e6aeb16
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406682"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="03688-103">ASP.NET Core Security の概要</span><span class="sxs-lookup"><span data-stu-id="03688-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="03688-104">ASP.NET Core を使用することで、開発者はアプリのセキュリティを簡単に構成して管理することができます。</span><span class="sxs-lookup"><span data-stu-id="03688-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="03688-105">ASP.NET Core には、認証、承認、データ保護、HTTPS の適用、アプリ シークレット、リクエスト フォージェリの対策保護、および CORS を管理するための機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="03688-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="03688-106">これらのセキュリティ機能を使用すれば、堅牢かつセキュアな ASP.NET Core アプリを構築できます。</span><span class="sxs-lookup"><span data-stu-id="03688-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="03688-107">ASP.NET Core セキュリティ機能</span><span class="sxs-lookup"><span data-stu-id="03688-107">ASP.NET Core security features</span></span>

<span data-ttu-id="03688-108">ASP.NET Core では、組み込みの ID プロバイダーを含むアプリをセキュリティで保護するための多くのツールとライブラリが提供されますが、Facebook、Twitter、LinkedIn などのサードパーティの ID サービスを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="03688-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="03688-109">ASP.NET Core では、コードで公開せずに機密情報を格納して使用する方法である、アプリ シークレットを簡単に管理できます。</span><span class="sxs-lookup"><span data-stu-id="03688-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="03688-110">認証と承認</span><span class="sxs-lookup"><span data-stu-id="03688-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="03688-111">認証とは、ユーザーが提供した資格情報をオペレーティング システム、データベース、アプリまたはリソースに格納されているものと比較するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="03688-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="03688-112">資格情報が一致した場合、ユーザーは正常に認証され、承認プロセス中に、承認されたアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="03688-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="03688-113">承認とは、ユーザーに許可する実行内容を決定するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="03688-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="03688-114">また、認証はサーバー、データベース、アプリまたはリソースなどの領域に入る方法と見なすことができます。一方、承認はユーザーがその領域 (サーバー、データベース、またはアプリ) 内のいずかのオブジェクトに対して実行できるアクションです。</span><span class="sxs-lookup"><span data-stu-id="03688-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="03688-115">ソフトウェアの一般的な脆弱性</span><span class="sxs-lookup"><span data-stu-id="03688-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="03688-116">ASP.NET Core および EF には、アプリをセキュリティで保護し、セキュリティ違反を防止するのに役立つ機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="03688-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="03688-117">以下にリストされているリンクから、Web アプリの最も一般的なセキュリティの脆弱性を回避するための手法の詳細を示すドキュメントにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="03688-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="03688-118">クロスサイト スクリプト攻撃</span><span class="sxs-lookup"><span data-stu-id="03688-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="03688-119">SQL インジェクション攻撃</span><span class="sxs-lookup"><span data-stu-id="03688-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="03688-120">クロスサイト リクエスト フォージェリ (CSRF)</span><span class="sxs-lookup"><span data-stu-id="03688-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="03688-121">オープン リダイレクト攻撃</span><span class="sxs-lookup"><span data-stu-id="03688-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="03688-122">この他にも知っておく必要がある脆弱性はあります。</span><span class="sxs-lookup"><span data-stu-id="03688-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="03688-123">詳細については、目次の**セキュリティと Identity** のセクションにある他の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03688-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
