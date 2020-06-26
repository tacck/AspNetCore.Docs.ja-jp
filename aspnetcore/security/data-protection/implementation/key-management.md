---
title: ASP.NET Core でのキー管理
author: rick-anderson
description: ASP.NET Core データ保護キー管理 Api の実装の詳細について説明します。
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 68913d13c97ba6be73dabf79e03c146a37388ad3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408943"
---
# <a name="key-management-in-aspnet-core"></a><span data-ttu-id="c47d6-103">ASP.NET Core でのキー管理</span><span class="sxs-lookup"><span data-stu-id="c47d6-103">Key management in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-management"></a>

<span data-ttu-id="c47d6-104">データ保護システムは、ペイロードの保護と保護解除に使用されるマスターキーの有効期間を自動的に管理します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-104">The data protection system automatically manages the lifetime of master keys used to protect and unprotect payloads.</span></span> <span data-ttu-id="c47d6-105">各キーは、次の4つの段階のいずれかに存在することができます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-105">Each key can exist in one of four stages:</span></span>

* <span data-ttu-id="c47d6-106">Created-キーはキーリングに存在しますが、まだアクティブ化されていません。</span><span class="sxs-lookup"><span data-stu-id="c47d6-106">Created - the key exists in the key ring but has not yet been activated.</span></span> <span data-ttu-id="c47d6-107">キーがこのキーリングを使用しているすべてのコンピューターに伝達されるまでに十分な時間が経過するまで、新しい保護操作には使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c47d6-107">The key shouldn't be used for new Protect operations until sufficient time has elapsed that the key has had a chance to propagate to all machines that are consuming this key ring.</span></span>

* <span data-ttu-id="c47d6-108">アクティブ-キーはキーリングに存在し、すべての新しい保護操作に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-108">Active - the key exists in the key ring and should be used for all new Protect operations.</span></span>

* <span data-ttu-id="c47d6-109">期限切れ-キーは自然な有効期間を実行し、新しい保護操作には使用できなくなります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-109">Expired - the key has run its natural lifetime and should no longer be used for new Protect operations.</span></span>

* <span data-ttu-id="c47d6-110">[失効]-キーが侵害され、新しい保護操作に使用されないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-110">Revoked - the key is compromised and must not be used for new Protect operations.</span></span>

<span data-ttu-id="c47d6-111">受信したペイロードの保護を解除するために、作成されたキー、アクティブなキー、および期限切れのキーがすべて使用される</span><span class="sxs-lookup"><span data-stu-id="c47d6-111">Created, active, and expired keys may all be used to unprotect incoming payloads.</span></span> <span data-ttu-id="c47d6-112">既定では、失効したキーを使用してペイロードの保護を解除することはできませんが、アプリケーション開発者は必要に応じて[この動作をオーバーライド](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect)できます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-112">Revoked keys by default may not be used to unprotect payloads, but the application developer can [override this behavior](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) if necessary.</span></span>

>[!WARNING]
> <span data-ttu-id="c47d6-113">開発者がキーリングからキーを削除しようとしている可能性があります (たとえば、対応するファイルをファイルシステムから削除します)。</span><span class="sxs-lookup"><span data-stu-id="c47d6-113">The developer might be tempted to delete a key from the key ring (e.g., by deleting the corresponding file from the file system).</span></span> <span data-ttu-id="c47d6-114">その時点で、キーによって保護されているすべてのデータは完全に undecipherable されており、取り消し済みキーがある場合のような緊急オーバーライドはありません。</span><span class="sxs-lookup"><span data-stu-id="c47d6-114">At that point, all data protected by the key is permanently undecipherable, and there's no emergency override like there's with revoked keys.</span></span> <span data-ttu-id="c47d6-115">キーを削除することは本当に破壊的な動作であり、そのため、データ保護システムはこの操作を実行するためのファーストクラス API を公開しません。</span><span class="sxs-lookup"><span data-stu-id="c47d6-115">Deleting a key is truly destructive behavior, and consequently the data protection system exposes no first-class API for performing this operation.</span></span>

## <a name="default-key-selection"></a><span data-ttu-id="c47d6-116">既定のキーの選択</span><span class="sxs-lookup"><span data-stu-id="c47d6-116">Default key selection</span></span>

<span data-ttu-id="c47d6-117">データ保護システムは、バッキングリポジトリからキーリングを読み取るときに、キーリングから "default" キーの検索を試みます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-117">When the data protection system reads the key ring from the backing repository, it will attempt to locate a "default" key from the key ring.</span></span> <span data-ttu-id="c47d6-118">既定のキーは、新しい保護操作に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-118">The default key is used for new Protect operations.</span></span>

<span data-ttu-id="c47d6-119">一般的なヒューリスティックは、データ保護システムが、最新のアクティブ化日を既定のキーとしてキーを選択することです。</span><span class="sxs-lookup"><span data-stu-id="c47d6-119">The general heuristic is that the data protection system chooses the key with the most recent activation date as the default key.</span></span> <span data-ttu-id="c47d6-120">(サーバー間のクロックスキューを可能にするための小さなファッジ要因があります)。キーの有効期限が切れた場合や失効した場合、またアプリケーションが自動キー生成を無効にしていない場合は、次の[キーの有効期限とローリング](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration)ポリシーに従って、即時ライセンス認証を使用して新しいキーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-120">(There's a small fudge factor to allow for server-to-server clock skew.) If the key is expired or revoked, and if the application has not disabled automatic key generation, then a new key will be generated with immediate activation per the [key expiration and rolling](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) policy below.</span></span>

<span data-ttu-id="c47d6-121">データ保護システムが別のキーにフォールバックするのではなく、直ちに新しいキーを生成するのは、新しいキーの生成が、新しいキーの前にアクティブ化されたすべてのキーの暗黙的な有効期限として扱われるためです。</span><span class="sxs-lookup"><span data-stu-id="c47d6-121">The reason the data protection system generates a new key immediately rather than falling back to a different key is that new key generation should be treated as an implicit expiration of all keys that were activated prior to the new key.</span></span> <span data-ttu-id="c47d6-122">一般的な考え方としては、新しいキーが古いキーよりも異なるアルゴリズムまたは暗号化された保存メカニズムを使用して構成されている可能性があります。また、システムは最新の構成を優先する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-122">The general idea is that new keys may have been configured with different algorithms or encryption-at-rest mechanisms than old keys, and the system should prefer the current configuration over falling back.</span></span>

<span data-ttu-id="c47d6-123">例外が発生しています。</span><span class="sxs-lookup"><span data-stu-id="c47d6-123">There's an exception.</span></span> <span data-ttu-id="c47d6-124">アプリケーション開発者が[自動キー生成を無効](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)にしている場合は、データ保護システムが既定のキーとして何かを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-124">If the application developer has [disabled automatic key generation](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), then the data protection system must choose something as the default key.</span></span> <span data-ttu-id="c47d6-125">このフォールバックシナリオでは、システムは、最新のライセンス認証日を持つ非失効キーを選択します。これは、クラスター内の他のコンピューターに反映されるまでの時間があるキーに対して設定されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-125">In this fallback scenario, the system will choose the non-revoked key with the most recent activation date, with preference given to keys that have had time to propagate to other machines in the cluster.</span></span> <span data-ttu-id="c47d6-126">フォールバックシステムによって、有効期限が切れた既定のキーが最終的に選択される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-126">The fallback system may end up choosing an expired default key as a result.</span></span> <span data-ttu-id="c47d6-127">フォールバックシステムは、既定のキーとして取り消しキーを選択することはありません。キーリングが空であるか、すべてのキーが取り消されている場合、初期化時にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-127">The fallback system will never choose a revoked key as the default key, and if the key ring is empty or every key has been revoked then the system will produce an error upon initialization.</span></span>

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a><span data-ttu-id="c47d6-128">キーの有効期限とローリング</span><span class="sxs-lookup"><span data-stu-id="c47d6-128">Key expiration and rolling</span></span>

<span data-ttu-id="c47d6-129">キーが作成されると、自動的に {now + 2 日} のライセンス認証日と {now + 90 日} の有効期限が指定されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-129">When a key is created, it's automatically given an activation date of { now + 2 days } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="c47d6-130">アクティブ化が開始されるまでの2日間の遅延により、キーがシステム全体に反映されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-130">The 2-day delay before activation gives the key time to propagate through the system.</span></span> <span data-ttu-id="c47d6-131">つまり、バッキングストアを指す他のアプリケーションが、次回の自動更新期間にキーを確認できるようになります。これにより、キーリングがアクティブになると、それを使用する必要があるすべてのアプリケーションに伝達される可能性が最大になります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-131">That is, it allows other applications pointing at the backing store to observe the key at their next auto-refresh period, thus maximizing the chances that when the key ring does become active it has propagated to all applications that might need to use it.</span></span>

<span data-ttu-id="c47d6-132">既定のキーの有効期限が2日以内に切れ、キーリングに既定のキーの有効期限が切れたときにアクティブになるキーがない場合、データ保護システムは自動的にキーリングに新しいキーを保持します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-132">If the default key will expire within 2 days and if the key ring doesn't already have a key that will be active upon expiration of the default key, then the data protection system will automatically persist a new key to the key ring.</span></span> <span data-ttu-id="c47d6-133">この新しいキーのライセンス認証日は {既定のキーの有効期限} で、有効期限は {now + 90 日} です。</span><span class="sxs-lookup"><span data-stu-id="c47d6-133">This new key has an activation date of { default key's expiration date } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="c47d6-134">これにより、システムはサービスを中断することなく定期的に自動的にキーをロールすることができます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-134">This allows the system to automatically roll keys on a regular basis with no interruption of service.</span></span>

<span data-ttu-id="c47d6-135">即時ライセンス認証を使用してキーが作成される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-135">There might be circumstances where a key will be created with immediate activation.</span></span> <span data-ttu-id="c47d6-136">たとえば、アプリケーションが一度も実行されず、キーリング内のすべてのキーの有効期限が切れている場合などです。</span><span class="sxs-lookup"><span data-stu-id="c47d6-136">One example would be when the application hasn't run for a time and all keys in the key ring are expired.</span></span> <span data-ttu-id="c47d6-137">この場合、通常の2日間のライセンス認証の遅延なしに、キーに {now} というライセンス認証日が付与されます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-137">When this happens, the key is given an activation date of { now } without the normal 2-day activation delay.</span></span>

<span data-ttu-id="c47d6-138">既定のキーの有効期間は90日ですが、次の例のように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-138">The default key lifetime is 90 days, though this is configurable as in the following example.</span></span>

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

<span data-ttu-id="c47d6-139">また、管理者は既定のシステム全体を変更することもできます。ただし、の明示的な呼び出しで `SetDefaultKeyLifetime` は、システム全体のポリシーがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-139">An administrator can also change the default system-wide, though an explicit call to `SetDefaultKeyLifetime` will override any system-wide policy.</span></span> <span data-ttu-id="c47d6-140">既定のキーの有効期間を7日より短くすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c47d6-140">The default key lifetime cannot be shorter than 7 days.</span></span>

## <a name="automatic-key-ring-refresh"></a><span data-ttu-id="c47d6-141">キーリングの自動更新</span><span class="sxs-lookup"><span data-stu-id="c47d6-141">Automatic key ring refresh</span></span>

<span data-ttu-id="c47d6-142">データ保護システムが初期化されると、基になるリポジトリからキーリングが読み取られ、メモリにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-142">When the data protection system initializes, it reads the key ring from the underlying repository and caches it in memory.</span></span> <span data-ttu-id="c47d6-143">このキャッシュにより、バッキングストアに到達せずに、保護と保護解除の操作を続行できます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-143">This cache allows Protect and Unprotect operations to proceed without hitting the backing store.</span></span> <span data-ttu-id="c47d6-144">システムは、ほぼ24時間ごとに、または現在の既定のキーの有効期限が切れる前に、バックアップストアに変更がないかを自動的に確認します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-144">The system will automatically check the backing store for changes approximately every 24 hours or when the current default key expires, whichever comes first.</span></span>

>[!WARNING]
> <span data-ttu-id="c47d6-145">開発者は、キー管理 Api を直接使用する必要はほとんどありません (これがある場合)。</span><span class="sxs-lookup"><span data-stu-id="c47d6-145">Developers should very rarely (if ever) need to use the key management APIs directly.</span></span> <span data-ttu-id="c47d6-146">データ保護システムは、前述のように自動キー管理を実行します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-146">The data protection system will perform automatic key management as described above.</span></span>

<span data-ttu-id="c47d6-147">データ保護システムは、 `IKeyManager` キーリングの検査と変更を行うために使用できるインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-147">The data protection system exposes an interface `IKeyManager` that can be used to inspect and make changes to the key ring.</span></span> <span data-ttu-id="c47d6-148">のインスタンスを提供した DI システムでは、のインスタンスを使用する `IDataProtectionProvider` こともでき `IKeyManager` ます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-148">The DI system that provided the instance of `IDataProtectionProvider` can also provide an instance of `IKeyManager` for your consumption.</span></span> <span data-ttu-id="c47d6-149">または、 `IKeyManager` 次の例のように、から直接をプルすることもでき `IServiceProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-149">Alternatively, you can pull the `IKeyManager` straight from the `IServiceProvider` as in the example below.</span></span>

<span data-ttu-id="c47d6-150">キーリングを変更する操作 (新しいキーを明示的に作成するか、または失効を実行する) は、メモリ内キャッシュを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c47d6-150">Any operation which modifies the key ring (creating a new key explicitly or performing a revocation) will invalidate the in-memory cache.</span></span> <span data-ttu-id="c47d6-151">次にまたはを呼び出すと、 `Protect` `Unprotect` データ保護システムがキーリングを再度読み込み、キャッシュを再作成します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-151">The next call to `Protect` or `Unprotect` will cause the data protection system to reread the key ring and recreate the cache.</span></span>

<span data-ttu-id="c47d6-152">次のサンプルでは、インターフェイスを使用して、 `IKeyManager` キーリングを検査および操作します。これには、既存のキーの取り消しや、新しいキーの手動生成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-152">The sample below demonstrates using the `IKeyManager` interface to inspect and manipulate the key ring, including revoking existing keys and generating a new key manually.</span></span>

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a><span data-ttu-id="c47d6-153">キー記憶域</span><span class="sxs-lookup"><span data-stu-id="c47d6-153">Key storage</span></span>

<span data-ttu-id="c47d6-154">データ保護システムには、適切なキーの保存場所と保存時の暗号化メカニズムが自動的に推測されるというヒューリスティックがあります。</span><span class="sxs-lookup"><span data-stu-id="c47d6-154">The data protection system has a heuristic whereby it attempts to deduce an appropriate key storage location and encryption-at-rest mechanism automatically.</span></span> <span data-ttu-id="c47d6-155">キーの永続化メカニズムは、アプリの開発者によって構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c47d6-155">The key persistence mechanism is also configurable by the app developer.</span></span> <span data-ttu-id="c47d6-156">次のドキュメントでは、これらのメカニズムのインボックス実装について説明します。</span><span class="sxs-lookup"><span data-stu-id="c47d6-156">The following documents discuss the in-box implementations of these mechanisms:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
