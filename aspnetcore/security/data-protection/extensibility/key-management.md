---
title: ASP.NET Core のキー管理機能拡張
author: rick-anderson
description: ASP.NET Core データ保護のキー管理の拡張性について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 5f55b56bd35a583e1f078a5a281788b68412e4f7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021693"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="620d0-103">ASP.NET Core のキー管理機能拡張</span><span class="sxs-lookup"><span data-stu-id="620d0-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="620d0-104">このセクションを読む前に、「[キー管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)」セクションをお読みください。このセクションでは、これらの api の基本的な概念について説明します。</span><span class="sxs-lookup"><span data-stu-id="620d0-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="620d0-105">次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="620d0-106">キー</span><span class="sxs-lookup"><span data-stu-id="620d0-106">Key</span></span>

<span data-ttu-id="620d0-107">`IKey`インターフェイスは、cryptosystem のキーの基本的な表現です。</span><span class="sxs-lookup"><span data-stu-id="620d0-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="620d0-108">ここでは、"暗号化キーマテリアル" の意味ではなく、抽象的な意味で用語キーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="620d0-109">キーには、次のプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="620d0-109">A key has the following properties:</span></span>

* <span data-ttu-id="620d0-110">アクティブ化、作成、有効期限</span><span class="sxs-lookup"><span data-stu-id="620d0-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="620d0-111">失効状態</span><span class="sxs-lookup"><span data-stu-id="620d0-111">Revocation status</span></span>

* <span data-ttu-id="620d0-112">キー識別子 (GUID)</span><span class="sxs-lookup"><span data-stu-id="620d0-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="620d0-113">さらに、は、 `IKey` `CreateEncryptor` このキーに関連付けられている[I認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できるメソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="620d0-114">さらに、は、 `IKey` `CreateEncryptorInstance` このキーに関連付けられている[I認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できるメソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="620d0-115">インスタンスから未加工の暗号化マテリアルを取得する API はありません `IKey` 。</span><span class="sxs-lookup"><span data-stu-id="620d0-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="620d0-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="620d0-116">IKeyManager</span></span>

<span data-ttu-id="620d0-117">インターフェイスは、 `IKeyManager` 一般的なキーの格納、取得、および操作を行うオブジェクトを表します。</span><span class="sxs-lookup"><span data-stu-id="620d0-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="620d0-118">次の3つの高レベルの操作を公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="620d0-119">新しいキーを作成し、ストレージに保存します。</span><span class="sxs-lookup"><span data-stu-id="620d0-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="620d0-120">ストレージからすべてのキーを取得します。</span><span class="sxs-lookup"><span data-stu-id="620d0-120">Get all keys from storage.</span></span>

* <span data-ttu-id="620d0-121">1つまたは複数のキーを失効させ、失効情報をストレージに保持します。</span><span class="sxs-lookup"><span data-stu-id="620d0-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="620d0-122">の記述 `IKeyManager` は非常に高度なタスクであり、ほとんどの開発者がそれを試みることはできません。</span><span class="sxs-lookup"><span data-stu-id="620d0-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="620d0-123">代わりに、ほとんどの開発者は、 [Xmlkeymanager](#xmlkeymanager)クラスによって提供される機能を活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="620d0-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="620d0-124">XmlKeyManager</span></span>

<span data-ttu-id="620d0-125">`XmlKeyManager`型は、のインボックスの具象実装 `IKeyManager` です。</span><span class="sxs-lookup"><span data-stu-id="620d0-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="620d0-126">これには、キーエスクローや保存時のキーの暗号化など、便利な機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="620d0-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="620d0-127">このシステムのキーは、XML 要素 (具体的には[XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) として表されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="620d0-128">`XmlKeyManager`タスクを実行する過程で、他のいくつかのコンポーネントに依存します。</span><span class="sxs-lookup"><span data-stu-id="620d0-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="620d0-129">`AlgorithmConfiguration`。新しいキーによって使用されるアルゴリズムを指定します。</span><span class="sxs-lookup"><span data-stu-id="620d0-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="620d0-130">`IXmlRepository`。ストレージでキーが保存される場所を制御します。</span><span class="sxs-lookup"><span data-stu-id="620d0-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="620d0-131">`IXmlEncryptor`[省略可能]。保存時のキーの暗号化を許可します。</span><span class="sxs-lookup"><span data-stu-id="620d0-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="620d0-132">`IKeyEscrowSink`[省略可能]。キーエスクローサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="620d0-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="620d0-133">`IXmlRepository`。ストレージでキーが保存される場所を制御します。</span><span class="sxs-lookup"><span data-stu-id="620d0-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="620d0-134">`IXmlEncryptor`[省略可能]。保存時のキーの暗号化を許可します。</span><span class="sxs-lookup"><span data-stu-id="620d0-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="620d0-135">`IKeyEscrowSink`[省略可能]。キーエスクローサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="620d0-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="620d0-136">これらのコンポーネントがどのように結線されるかを示す概略図を次に示し `XmlKeyManager` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation2.png)

<span data-ttu-id="620d0-138">*キーの作成/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="620d0-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="620d0-139">の実装では、 `CreateNewKey` `AlgorithmConfiguration` コンポーネントを使用して一意のを作成 `IAuthenticatedEncryptorDescriptor` し、その後 XML としてシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="620d0-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="620d0-140">キーエスクローシンクが存在する場合は、未加工の (暗号化されていない) XML が、長期保存のためにシンクに提供されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="620d0-141">暗号化されていない XML は、(必要に応じて) を介して実行され、 `IXmlEncryptor` 暗号化された xml ドキュメントを生成します。</span><span class="sxs-lookup"><span data-stu-id="620d0-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="620d0-142">この暗号化されたドキュメントは、を使用して長期的なストレージに保存され `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="620d0-143">(が構成されていない場合、暗号化されて `IXmlEncryptor` いないドキュメントはに保存され `IXmlRepository` ます)。</span><span class="sxs-lookup"><span data-stu-id="620d0-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![キーの取得](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation1.png)

<span data-ttu-id="620d0-146">*キーの作成/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="620d0-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="620d0-147">の実装では、 `CreateNewKey` `IAuthenticatedEncryptorConfiguration` コンポーネントを使用して一意のを作成 `IAuthenticatedEncryptorDescriptor` し、その後 XML としてシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="620d0-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="620d0-148">キーエスクローシンクが存在する場合は、未加工の (暗号化されていない) XML が、長期保存のためにシンクに提供されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="620d0-149">暗号化されていない XML は、(必要に応じて) を介して実行され、 `IXmlEncryptor` 暗号化された xml ドキュメントを生成します。</span><span class="sxs-lookup"><span data-stu-id="620d0-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="620d0-150">この暗号化されたドキュメントは、を使用して長期的なストレージに保存され `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="620d0-151">(が構成されていない場合、暗号化されて `IXmlEncryptor` いないドキュメントはに保存され `IXmlRepository` ます)。</span><span class="sxs-lookup"><span data-stu-id="620d0-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![キーの取得](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="620d0-153">*キーの取得/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="620d0-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="620d0-154">の実装では、 `GetAllKeys` キーと失効を表す XML ドキュメントが、基になるから読み取られ `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="620d0-155">これらのドキュメントが暗号化されている場合は、システムによって自動的に暗号化が解除されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="620d0-156">`XmlKeyManager`は、ドキュメントを逆シリアル化してインスタンスに戻すための適切なインスタンスを作成し `IAuthenticatedEncryptorDescriptorDeserializer` `IAuthenticatedEncryptorDescriptor` 、次に個々のインスタンスにラップし `IKey` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="620d0-157">このインスタンスのコレクション `IKey` は、呼び出し元に返されます。</span><span class="sxs-lookup"><span data-stu-id="620d0-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="620d0-158">特定の XML 要素の詳細については、「[キーストレージ形式」ドキュメント](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="620d0-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="620d0-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-159">IXmlRepository</span></span>

<span data-ttu-id="620d0-160">インターフェイスは、 `IXmlRepository` バッキングストアに対して xml を永続化したり、xml を取得したりできる型を表します。</span><span class="sxs-lookup"><span data-stu-id="620d0-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="620d0-161">次の2つの Api を公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-161">It exposes two APIs:</span></span>

* <span data-ttu-id="620d0-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="620d0-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="620d0-163">の実装で `IXmlRepository` は、それらを渡す XML を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="620d0-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="620d0-164">XML ドキュメントを不透明として扱い、より高いレイヤーでドキュメントの生成と解析について心配する必要があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="620d0-165">を実装する具象型には、次の4つが組み込まれてい `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="620d0-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="620d0-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="620d0-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="620d0-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="620d0-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="620d0-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="620d0-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="620d0-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="620d0-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="620d0-174">詳細については、「[キー記憶域プロバイダー」ドキュメント](xref:security/data-protection/implementation/key-storage-providers)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="620d0-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="620d0-175">カスタムの登録 `IXmlRepository` は、別のバッキングストア (Azure Table Storage など) を使用する場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="620d0-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="620d0-176">既定のリポジトリアプリケーション全体を変更するには、カスタムインスタンスを登録し `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="620d0-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="620d0-177">IXmlEncryptor</span></span>

<span data-ttu-id="620d0-178">インターフェイスは、 `IXmlEncryptor` プレーンテキストの XML 要素を暗号化できる型を表します。</span><span class="sxs-lookup"><span data-stu-id="620d0-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="620d0-179">1つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-179">It exposes a single API:</span></span>

* <span data-ttu-id="620d0-180">Encrypt (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="620d0-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="620d0-181">シリアル化さ `IAuthenticatedEncryptorDescriptor` れたに "暗号化が必要" とマークされている要素が含まれている場合、 `XmlKeyManager` は、構成されたメソッドを通じてこれらの要素を実行 `IXmlEncryptor` し、 `Encrypt` のプレーンテキスト要素ではなく読んだり要素を保持し `IXmlRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="620d0-182">メソッドの出力 `Encrypt` は `EncryptedXmlInfo` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="620d0-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="620d0-183">このオブジェクトは、結果の読んだりと、対応する `XElement` `IXmlDecryptor` 要素の暗号を解除するために使用できるを表す型の両方を含むラッパーです。</span><span class="sxs-lookup"><span data-stu-id="620d0-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="620d0-184">を実装する具象型には、次の4つが組み込まれてい `IXmlEncryptor` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="620d0-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="620d0-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="620d0-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="620d0-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="620d0-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="620d0-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="620d0-188">NullXmlEncryptor 機能</span><span class="sxs-lookup"><span data-stu-id="620d0-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="620d0-189">詳細については、保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="620d0-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="620d0-190">既定のキー暗号化メカニズムのアプリケーション全体を変更するには、カスタムインスタンスを登録し `IXmlEncryptor` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="620d0-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="620d0-191">IXmlDecryptor</span></span>

<span data-ttu-id="620d0-192">インターフェイスは、を `IXmlDecryptor` `XElement` 介して読んだりされたを復号化する方法を認識する型を表し `IXmlEncryptor` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="620d0-193">1つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-193">It exposes a single API:</span></span>

* <span data-ttu-id="620d0-194">暗号化解除 (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="620d0-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="620d0-195">メソッドは、 `Decrypt` によって実行される暗号化を元に戻し `IXmlEncryptor.Encrypt` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="620d0-196">一般に、各具象実装には、 `IXmlEncryptor` 対応する具象実装があり `IXmlDecryptor` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="620d0-197">を実装する型 `IXmlDecryptor` には、次の2つのパブリックコンストラクターのいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="620d0-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="620d0-198">.ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="620d0-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="620d0-199">.ctor ()</span><span class="sxs-lookup"><span data-stu-id="620d0-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="620d0-200">`IServiceProvider`コンストラクターに渡されるは null でもかまいません。</span><span class="sxs-lookup"><span data-stu-id="620d0-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="620d0-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="620d0-201">IKeyEscrowSink</span></span>

<span data-ttu-id="620d0-202">インターフェイスは、 `IKeyEscrowSink` 機密情報のエスクローを実行できる型を表します。</span><span class="sxs-lookup"><span data-stu-id="620d0-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="620d0-203">シリアル化された記述子に機密情報 (暗号化マテリアルなど) が含まれている可能性があることを思い出してください。これは、 [IXmlEncryptor](#ixmlencryptor)型が最初に導入されたものです。</span><span class="sxs-lookup"><span data-stu-id="620d0-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="620d0-204">しかし、事故が発生し、キーリングが削除されたり、破損したりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="620d0-205">エスクローエスケープハッチを使用すると、構成済みの[IXmlEncryptor](#ixmlencryptor)によって変換される前に、未処理のシリアル化された XML にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="620d0-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="620d0-206">インターフェイスは、単一の API を公開します。</span><span class="sxs-lookup"><span data-stu-id="620d0-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="620d0-207">ストア (Guid キー Id、XElement 要素)</span><span class="sxs-lookup"><span data-stu-id="620d0-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="620d0-208">`IKeyEscrowSink`ビジネスポリシーと一貫性のあるセキュリティで保護された方法で提供される要素を処理するのは、実装によって異なります。</span><span class="sxs-lookup"><span data-stu-id="620d0-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="620d0-209">可能な実装の1つとして、エスクローシンクが、証明書の秘密キーがエスクローされている既知の企業の x.509 証明書を使用して XML 要素を暗号化することが考えられます。`CertificateXmlEncryptor`この型は、このをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="620d0-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="620d0-210">`IKeyEscrowSink`実装は、指定された要素を適切に永続化する役割も担います。</span><span class="sxs-lookup"><span data-stu-id="620d0-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="620d0-211">既定では、エスクローメカニズムは有効になっていませんが、サーバー管理者は[グローバルに構成](xref:security/data-protection/configuration/machine-wide-policy)することができます。</span><span class="sxs-lookup"><span data-stu-id="620d0-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="620d0-212">次のサンプルに示すように、メソッドを使用してプログラムで構成することもでき `IDataProtectionBuilder.AddKeyEscrowSink` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="620d0-213">`AddKeyEscrowSink` `IServiceCollection.AddSingleton` インスタンスはシングルトンとして使用されるため、メソッドオーバーロードはとのオーバーロードをミラー化し `IServiceCollection.AddInstance` `IKeyEscrowSink` ます。</span><span class="sxs-lookup"><span data-stu-id="620d0-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="620d0-214">複数のインスタンスが登録されている場合は `IKeyEscrowSink` 、キーの生成時にそれぞれが呼び出されます。そのため、キーを複数のメカニズムに同時にエスクローことができます。</span><span class="sxs-lookup"><span data-stu-id="620d0-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="620d0-215">インスタンスからマテリアルを読み取る API はありません `IKeyEscrowSink` 。</span><span class="sxs-lookup"><span data-stu-id="620d0-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="620d0-216">これは、エスクローメカニズムの設計理論と一貫性があります。これは、信頼された機関からキーマテリアルにアクセスできるようにするためのものであり、アプリケーション自体が信頼された機関ではないため、独自のエスクローマテリアルにアクセスできないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="620d0-217">次のサンプルコードは、 `IKeyEscrowSink` "CONTOSODomain Admins" のメンバーだけが回復できるように、キーがエスクローされるを作成して登録する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="620d0-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="620d0-218">このサンプルを実行するには、ドメインに参加している Windows 8/Windows Server 2012 コンピューターで、ドメインコントローラーが Windows Server 2012 以降である必要があります。</span><span class="sxs-lookup"><span data-stu-id="620d0-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
