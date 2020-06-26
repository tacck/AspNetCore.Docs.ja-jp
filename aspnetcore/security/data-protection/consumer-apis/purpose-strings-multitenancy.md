---
title: ASP.NET Core の目的階層とマルチテナント
author: rick-anderson
description: ASP.NET Core データ保護 Api に関連する、目的の文字列階層とマルチテナントについて説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 8f069da500e7bc06e4b8712fbf7b86d90a815758
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404380"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a><span data-ttu-id="6eb5d-103">ASP.NET Core の目的階層とマルチテナント</span><span class="sxs-lookup"><span data-stu-id="6eb5d-103">Purpose hierarchy and multi-tenancy in ASP.NET Core</span></span>

<span data-ttu-id="6eb5d-104">`IDataProtector`は暗黙的にもあるため `IDataProtectionProvider` 、目的を連結することができます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-104">Since an `IDataProtector` is also implicitly an `IDataProtectionProvider`, purposes can be chained together.</span></span> <span data-ttu-id="6eb5d-105">この意味で `provider.CreateProtector([ "purpose1", "purpose2" ])` は、はと同じです `provider.CreateProtector("purpose1").CreateProtector("purpose2")` 。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-105">In this sense, `provider.CreateProtector([ "purpose1", "purpose2" ])` is equivalent to `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.</span></span>

<span data-ttu-id="6eb5d-106">これにより、データ保護システムを通じて、いくつかの興味深い階層関係を実現できます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-106">This allows for some interesting hierarchical relationships through the data protection system.</span></span> <span data-ttu-id="6eb5d-107">前の例の[Contoso. Messaging. securemessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)コンポーネントでは、最初に `provider.CreateProtector("Contoso.Messaging.SecureMessage")` 1 回呼び出し、その結果をプライベートフィールドにキャッシュできます `_myProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-107">In the earlier example of [Contoso.Messaging.SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), the SecureMessage component can call `provider.CreateProtector("Contoso.Messaging.SecureMessage")` once up-front and cache the result into a private `_myProvider` field.</span></span> <span data-ttu-id="6eb5d-108">将来のプロテクターは、の呼び出しを使用 `_myProvider.CreateProtector("User: username")` して作成できます。これらのプロテクターは、個々のメッセージをセキュリティで保護するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-108">Future protectors can then be created via calls to `_myProvider.CreateProtector("User: username")`, and these protectors would be used for securing the individual messages.</span></span>

<span data-ttu-id="6eb5d-109">これを反転させることもできます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-109">This can also be flipped.</span></span> <span data-ttu-id="6eb5d-110">複数のテナントをホストする単一の論理アプリケーションがあるとします (CMS は妥当と思われます)。各テナントは独自の認証および状態管理システムを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-110">Consider a single logical application which hosts multiple tenants (a CMS seems reasonable), and each tenant can be configured with its own authentication and state management system.</span></span> <span data-ttu-id="6eb5d-111">包括的なアプリケーションには単一のマスタープロバイダーがあり、とを呼び出して、 `provider.CreateProtector("Tenant 1")` `provider.CreateProtector("Tenant 2")` 各テナントにデータ保護システムの分離されたスライスを与えます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-111">The umbrella application has a single master provider, and it calls `provider.CreateProtector("Tenant 1")` and `provider.CreateProtector("Tenant 2")` to give each tenant its own isolated slice of the data protection system.</span></span> <span data-ttu-id="6eb5d-112">その後、テナントは独自のプロテクターを独自のニーズに基づいて派生させることができますが、システム内の他のテナントと競合するプロテクターを作成するのがいかに難しいかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-112">The tenants could then derive their own individual protectors based on their own needs, but no matter how hard they try they cannot create protectors which collide with any other tenant in the system.</span></span> <span data-ttu-id="6eb5d-113">グラフィックでは、次のように表されます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-113">Graphically, this is represented as below.</span></span>

![マルチテナントの目的](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> <span data-ttu-id="6eb5d-115">これは、包括アプリケーションが個々のテナントに使用できる Api を制御し、テナントがサーバー上で任意のコードを実行できないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-115">This assumes the umbrella application controls which APIs are available to individual tenants and that tenants cannot execute arbitrary code on the server.</span></span> <span data-ttu-id="6eb5d-116">テナントが任意のコードを実行できる場合は、プライベートリフレクションを実行して分離の保証を解除するか、マスターキーマテリアルを直接読み取り、任意のサブキーを派生させることができます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-116">If a tenant can execute arbitrary code, they could perform private reflection to break the isolation guarantees, or they could just read the master keying material directly and derive whatever subkeys they desire.</span></span>

<span data-ttu-id="6eb5d-117">データ保護システムでは、既定の設定ではなく、既定の構成でマルチテナントの一種が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-117">The data protection system actually uses a sort of multi-tenancy in its default out-of-the-box configuration.</span></span> <span data-ttu-id="6eb5d-118">既定では、マスターキー生成情報は、ワーカープロセスアカウントのユーザープロファイルフォルダー (または、IIS アプリケーションプール id の場合はレジストリ) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-118">By default master keying material is stored in the worker process account's user profile folder (or the registry, for IIS application pool identities).</span></span> <span data-ttu-id="6eb5d-119">しかし、1つのアカウントを使用して複数のアプリケーションを実行することは、実際には非常に一般的です。そのため、これらすべてのアプリケーションは、マスターキー生成情報を共有することになります。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-119">But it's actually fairly common to use a single account to run multiple applications, and thus all these applications would end up sharing the master keying material.</span></span> <span data-ttu-id="6eb5d-120">これを解決するには、データ保護システムによって、一意のアプリケーションごとの識別子が、全体的な目的のチェーンの最初の要素として自動的に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-120">To solve this, the data protection system automatically inserts a unique-per-application identifier as the first element in the overall purpose chain.</span></span> <span data-ttu-id="6eb5d-121">この暗黙的な目的は、各アプリケーションをシステム内の一意のテナントとして効果的に扱うことによって、[個々のアプリケーション](xref:security/data-protection/configuration/overview#per-application-isolation)を相互に分離するためのものであり、保護機能の作成プロセスは上記のイメージと同じです。</span><span class="sxs-lookup"><span data-stu-id="6eb5d-121">This implicit purpose serves to [isolate individual applications](xref:security/data-protection/configuration/overview#per-application-isolation) from one another by effectively treating each application as a unique tenant within the system, and the protector creation process looks identical to the image above.</span></span>
