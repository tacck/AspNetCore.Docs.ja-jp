---
title: ASP.NET Core でのキー管理
author: rick-anderson
description: ASP.NET Core データ保護キー管理 Api の実装の詳細について説明します。
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 7cc0c7203dbc7b607bb7359990b75b000bb09b7e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634398"
---
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core でのキー管理

<a name="data-protection-implementation-key-management"></a>

データ保護システムは、ペイロードの保護と保護解除に使用されるマスターキーの有効期間を自動的に管理します。 各キーは、次の4つの段階のいずれかに存在することができます。

* Created-キーはキーリングに存在しますが、まだアクティブ化されていません。 キーがこのキーリングを使用しているすべてのコンピューターに伝達されるまでに十分な時間が経過するまで、新しい保護操作には使用しないでください。

* アクティブ-キーはキーリングに存在し、すべての新しい保護操作に使用する必要があります。

* 期限切れ-キーは自然な有効期間を実行し、新しい保護操作には使用できなくなります。

* [失効]-キーが侵害され、新しい保護操作に使用されないようにする必要があります。

受信したペイロードの保護を解除するために、作成されたキー、アクティブなキー、および期限切れのキーがすべて使用される 既定では、失効したキーを使用してペイロードの保護を解除することはできませんが、アプリケーション開発者は必要に応じて [この動作をオーバーライド](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) できます。

>[!WARNING]
> 開発者がキーリングからキーを削除しようとしている可能性があります (たとえば、対応するファイルをファイルシステムから削除します)。 その時点で、キーによって保護されているすべてのデータは完全に undecipherable されており、取り消し済みキーがある場合のような緊急オーバーライドはありません。 キーを削除することは本当に破壊的な動作であり、そのため、データ保護システムはこの操作を実行するためのファーストクラス API を公開しません。

## <a name="default-key-selection"></a>既定のキーの選択

データ保護システムは、バッキングリポジトリからキーリングを読み取るときに、キーリングから "default" キーの検索を試みます。 既定のキーは、新しい保護操作に使用されます。

一般的なヒューリスティックは、データ保護システムが、最新のアクティブ化日を既定のキーとしてキーを選択することです。 (サーバー間のクロックスキューを可能にするための小さなファッジ要因があります)。キーの有効期限が切れた場合や失効した場合、またアプリケーションが自動キー生成を無効にしていない場合は、次の [キーの有効期限とローリング](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) ポリシーに従って、即時ライセンス認証を使用して新しいキーが生成されます。

データ保護システムが別のキーにフォールバックするのではなく、直ちに新しいキーを生成するのは、新しいキーの生成が、新しいキーの前にアクティブ化されたすべてのキーの暗黙的な有効期限として扱われるためです。 一般的な考え方としては、新しいキーが古いキーよりも異なるアルゴリズムまたは暗号化された保存メカニズムを使用して構成されている可能性があります。また、システムは最新の構成を優先する必要があります。

例外が発生しています。 アプリケーション開発者が [自動キー生成を無効](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)にしている場合は、データ保護システムが既定のキーとして何かを選択する必要があります。 このフォールバックシナリオでは、システムは、最新のライセンス認証日を持つ非失効キーを選択します。これは、クラスター内の他のコンピューターに反映されるまでの時間があるキーに対して設定されます。 フォールバックシステムによって、有効期限が切れた既定のキーが最終的に選択される可能性があります。 フォールバックシステムは、既定のキーとして取り消しキーを選択することはありません。キーリングが空であるか、すべてのキーが取り消されている場合、初期化時にエラーが発生します。

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>キーの有効期限とローリング

キーが作成されると、自動的に {now + 2 日} のライセンス認証日と {now + 90 日} の有効期限が指定されます。 アクティブ化が開始されるまでの2日間の遅延により、キーがシステム全体に反映されます。 つまり、バッキングストアを指す他のアプリケーションが、次回の自動更新期間にキーを確認できるようになります。これにより、キーリングがアクティブになると、それを使用する必要があるすべてのアプリケーションに伝達される可能性が最大になります。

既定のキーの有効期限が2日以内に切れ、キーリングに既定のキーの有効期限が切れたときにアクティブになるキーがない場合、データ保護システムは自動的にキーリングに新しいキーを保持します。 この新しいキーのライセンス認証日は {既定のキーの有効期限} で、有効期限は {now + 90 日} です。 これにより、システムはサービスを中断することなく定期的に自動的にキーをロールすることができます。

即時ライセンス認証を使用してキーが作成される場合があります。 たとえば、アプリケーションが一度も実行されず、キーリング内のすべてのキーの有効期限が切れている場合などです。 この場合、通常の2日間のライセンス認証の遅延なしに、キーに {now} というライセンス認証日が付与されます。

既定のキーの有効期間は90日ですが、次の例のように構成することもできます。

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

また、管理者は既定のシステム全体を変更することもできます。ただし、の明示的な呼び出しで `SetDefaultKeyLifetime` は、システム全体のポリシーがオーバーライドされます。 既定のキーの有効期間を7日より短くすることはできません。

## <a name="automatic-key-ring-refresh"></a>キーリングの自動更新

データ保護システムが初期化されると、基になるリポジトリからキーリングが読み取られ、メモリにキャッシュされます。 このキャッシュにより、バッキングストアに到達せずに、保護と保護解除の操作を続行できます。 システムは、ほぼ24時間ごとに、または現在の既定のキーの有効期限が切れる前に、バックアップストアに変更がないかを自動的に確認します。

>[!WARNING]
> 開発者は、キー管理 Api を直接使用する必要はほとんどありません (これがある場合)。 データ保護システムは、前述のように自動キー管理を実行します。

データ保護システムは、 `IKeyManager` キーリングの検査と変更を行うために使用できるインターフェイスを公開します。 のインスタンスを提供した DI システムでは、のインスタンスを使用する `IDataProtectionProvider` こともでき `IKeyManager` ます。 または、 `IKeyManager` 次の例のように、から直接をプルすることもでき `IServiceProvider` ます。

キーリングを変更する操作 (新しいキーを明示的に作成するか、または失効を実行する) は、メモリ内キャッシュを無効にします。 次にまたはを呼び出すと、 `Protect` `Unprotect` データ保護システムがキーリングを再度読み込み、キャッシュを再作成します。

次のサンプルでは、インターフェイスを使用して、 `IKeyManager` キーリングを検査および操作します。これには、既存のキーの取り消しや、新しいキーの手動生成が含まれます。

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>キー記憶域

データ保護システムには、適切なキーの保存場所と保存時の暗号化メカニズムが自動的に推測されるというヒューリスティックがあります。 キーの永続化メカニズムは、アプリの開発者によって構成することもできます。 次のドキュメントでは、これらのメカニズムのインボックス実装について説明します。

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
