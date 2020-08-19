---
title: ASP.NET Core のキーの不変性とキー設定
author: rick-anderson
description: ASP.NET Core データ保護のキー不変 Api の実装の詳細について説明します。
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 32925a3ef6d52a7be3a852cefe670c15d545b490
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627625"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="66b4f-103">ASP.NET Core のキーの不変性とキー設定</span><span class="sxs-lookup"><span data-stu-id="66b4f-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="66b4f-104">オブジェクトがバッキングストアに永続化されると、その表現は永久に固定されます。</span><span class="sxs-lookup"><span data-stu-id="66b4f-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="66b4f-105">バッキングストアに新しいデータを追加することはできますが、既存のデータを変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="66b4f-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="66b4f-106">この動作の主な目的は、データの破損を防ぐことです。</span><span class="sxs-lookup"><span data-stu-id="66b4f-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="66b4f-107">この動作の1つの結果として、キーがバッキングストアに書き込まれると、変更できなくなります。</span><span class="sxs-lookup"><span data-stu-id="66b4f-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="66b4f-108">作成、アクティブ化、および有効期限の日付は変更できませんが、を使用して取り消すことができ `IKeyManager` ます。</span><span class="sxs-lookup"><span data-stu-id="66b4f-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="66b4f-109">さらに、基になるアルゴリズム情報、マスターキーマテリアル、および保存時の暗号化プロパティも変更できません。</span><span class="sxs-lookup"><span data-stu-id="66b4f-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="66b4f-110">キーの永続化に影響する設定が開発者によって変更された場合、これらの変更は、次にキーが生成されるまで有効になりません `IKeyManager.CreateNewKey` 。これは、またはデータ保護システム独自の [自動キー生成](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 動作を使用した明示的な呼び出しによって行われます。</span><span class="sxs-lookup"><span data-stu-id="66b4f-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="66b4f-111">キーの永続化に影響する設定は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="66b4f-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="66b4f-112">既定のキーの有効期間</span><span class="sxs-lookup"><span data-stu-id="66b4f-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="66b4f-113">保存時のキー暗号化メカニズム</span><span class="sxs-lookup"><span data-stu-id="66b4f-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="66b4f-114">キー内に含まれるアルゴリズム情報</span><span class="sxs-lookup"><span data-stu-id="66b4f-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="66b4f-115">これらの設定を次の自動キーのローリングタイムより前に開始する必要がある場合は、を明示的に呼び出して `IKeyManager.CreateNewKey` 新しいキーを強制的に作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="66b4f-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="66b4f-116">明示的なアクティベーション日 ({now + 2 日} は、変更が反映されるまでの時間を確保するための適切な目安です) と通話の有効期限を指定してください。</span><span class="sxs-lookup"><span data-stu-id="66b4f-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="66b4f-117">リポジトリに接しているすべてのアプリケーションでは、拡張メソッドで同じ設定を指定する必要があり `IDataProtectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="66b4f-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="66b4f-118">それ以外の場合、永続化されたキーのプロパティは、キー生成ルーチンを呼び出した特定のアプリケーションに依存します。</span><span class="sxs-lookup"><span data-stu-id="66b4f-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
