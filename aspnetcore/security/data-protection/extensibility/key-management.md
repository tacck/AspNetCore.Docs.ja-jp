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
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core のキー管理機能拡張

> [!TIP]
> このセクションを読む前に、「[キー管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)」セクションをお読みください。このセクションでは、これらの api の基本的な概念について説明します。

> [!WARNING]
> 次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。

## <a name="key"></a>キー

`IKey`インターフェイスは、cryptosystem のキーの基本的な表現です。 ここでは、"暗号化キーマテリアル" の意味ではなく、抽象的な意味で用語キーが使用されます。 キーには、次のプロパティがあります。

* アクティブ化、作成、有効期限

* 失効状態

* キー識別子 (GUID)

::: moniker range=">= aspnetcore-2.0"

さらに、は、 `IKey` `CreateEncryptor` このキーに関連付けられている[I認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できるメソッドを公開します。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

さらに、は、 `IKey` `CreateEncryptorInstance` このキーに関連付けられている[I認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できるメソッドを公開します。

::: moniker-end

> [!NOTE]
> インスタンスから未加工の暗号化マテリアルを取得する API はありません `IKey` 。

## <a name="ikeymanager"></a>IKeyManager

インターフェイスは、 `IKeyManager` 一般的なキーの格納、取得、および操作を行うオブジェクトを表します。 次の3つの高レベルの操作を公開します。

* 新しいキーを作成し、ストレージに保存します。

* ストレージからすべてのキーを取得します。

* 1つまたは複数のキーを失効させ、失効情報をストレージに保持します。

>[!WARNING]
> の記述 `IKeyManager` は非常に高度なタスクであり、ほとんどの開発者がそれを試みることはできません。 代わりに、ほとんどの開発者は、 [Xmlkeymanager](#xmlkeymanager)クラスによって提供される機能を活用する必要があります。

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager`型は、のインボックスの具象実装 `IKeyManager` です。 これには、キーエスクローや保存時のキーの暗号化など、便利な機能がいくつか用意されています。 このシステムのキーは、XML 要素 (具体的には[XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) として表されます。

`XmlKeyManager`タスクを実行する過程で、他のいくつかのコンポーネントに依存します。

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`。新しいキーによって使用されるアルゴリズムを指定します。

* `IXmlRepository`。ストレージでキーが保存される場所を制御します。

* `IXmlEncryptor`[省略可能]。保存時のキーの暗号化を許可します。

* `IKeyEscrowSink`[省略可能]。キーエスクローサービスを提供します。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`。ストレージでキーが保存される場所を制御します。

* `IXmlEncryptor`[省略可能]。保存時のキーの暗号化を許可します。

* `IKeyEscrowSink`[省略可能]。キーエスクローサービスを提供します。

::: moniker-end

これらのコンポーネントがどのように結線されるかを示す概略図を次に示し `XmlKeyManager` ます。

::: moniker range=">= aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation2.png)

*キーの作成/CreateNewKey*

の実装では、 `CreateNewKey` `AlgorithmConfiguration` コンポーネントを使用して一意のを作成 `IAuthenticatedEncryptorDescriptor` し、その後 XML としてシリアル化します。 キーエスクローシンクが存在する場合は、未加工の (暗号化されていない) XML が、長期保存のためにシンクに提供されます。 暗号化されていない XML は、(必要に応じて) を介して実行され、 `IXmlEncryptor` 暗号化された xml ドキュメントを生成します。 この暗号化されたドキュメントは、を使用して長期的なストレージに保存され `IXmlRepository` ます。 (が構成されていない場合、暗号化されて `IXmlEncryptor` いないドキュメントはに保存され `IXmlRepository` ます)。

![キーの取得](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation1.png)

*キーの作成/CreateNewKey*

の実装では、 `CreateNewKey` `IAuthenticatedEncryptorConfiguration` コンポーネントを使用して一意のを作成 `IAuthenticatedEncryptorDescriptor` し、その後 XML としてシリアル化します。 キーエスクローシンクが存在する場合は、未加工の (暗号化されていない) XML が、長期保存のためにシンクに提供されます。 暗号化されていない XML は、(必要に応じて) を介して実行され、 `IXmlEncryptor` 暗号化された xml ドキュメントを生成します。 この暗号化されたドキュメントは、を使用して長期的なストレージに保存され `IXmlRepository` ます。 (が構成されていない場合、暗号化されて `IXmlEncryptor` いないドキュメントはに保存され `IXmlRepository` ます)。

![キーの取得](key-management/_static/keyretrieval1.png)

::: moniker-end

*キーの取得/GetAllKeys*

の実装では、 `GetAllKeys` キーと失効を表す XML ドキュメントが、基になるから読み取られ `IXmlRepository` ます。 これらのドキュメントが暗号化されている場合は、システムによって自動的に暗号化が解除されます。 `XmlKeyManager`は、ドキュメントを逆シリアル化してインスタンスに戻すための適切なインスタンスを作成し `IAuthenticatedEncryptorDescriptorDeserializer` `IAuthenticatedEncryptorDescriptor` 、次に個々のインスタンスにラップし `IKey` ます。 このインスタンスのコレクション `IKey` は、呼び出し元に返されます。

特定の XML 要素の詳細については、「[キーストレージ形式」ドキュメント](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)を参照してください。

## <a name="ixmlrepository"></a>IXmlRepository

インターフェイスは、 `IXmlRepository` バッキングストアに対して xml を永続化したり、xml を取得したりできる型を表します。 次の2つの Api を公開します。

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

の実装で `IXmlRepository` は、それらを渡す XML を解析する必要はありません。 XML ドキュメントを不透明として扱い、より高いレイヤーでドキュメントの生成と解析について心配する必要があります。

を実装する具象型には、次の4つが組み込まれてい `IXmlRepository` ます。

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

詳細については、「[キー記憶域プロバイダー」ドキュメント](xref:security/data-protection/implementation/key-storage-providers)を参照してください。

カスタムの登録 `IXmlRepository` は、別のバッキングストア (Azure Table Storage など) を使用する場合に適しています。

既定のリポジトリアプリケーション全体を変更するには、カスタムインスタンスを登録し `IXmlRepository` ます。

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

インターフェイスは、 `IXmlEncryptor` プレーンテキストの XML 要素を暗号化できる型を表します。 1つの API を公開します。

* Encrypt (XElement plaintextElement): EncryptedXmlInfo

シリアル化さ `IAuthenticatedEncryptorDescriptor` れたに "暗号化が必要" とマークされている要素が含まれている場合、 `XmlKeyManager` は、構成されたメソッドを通じてこれらの要素を実行 `IXmlEncryptor` し、 `Encrypt` のプレーンテキスト要素ではなく読んだり要素を保持し `IXmlRepository` ます。 メソッドの出力 `Encrypt` は `EncryptedXmlInfo` オブジェクトです。 このオブジェクトは、結果の読んだりと、対応する `XElement` `IXmlDecryptor` 要素の暗号を解除するために使用できるを表す型の両方を含むラッパーです。

を実装する具象型には、次の4つが組み込まれてい `IXmlEncryptor` ます。

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor 機能](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

詳細については、保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)に関するドキュメントを参照してください。

既定のキー暗号化メカニズムのアプリケーション全体を変更するには、カスタムインスタンスを登録し `IXmlEncryptor` ます。

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

インターフェイスは、を `IXmlDecryptor` `XElement` 介して読んだりされたを復号化する方法を認識する型を表し `IXmlEncryptor` ます。 1つの API を公開します。

* 暗号化解除 (XElement encryptedElement): XElement

メソッドは、 `Decrypt` によって実行される暗号化を元に戻し `IXmlEncryptor.Encrypt` ます。 一般に、各具象実装には、 `IXmlEncryptor` 対応する具象実装があり `IXmlDecryptor` ます。

を実装する型 `IXmlDecryptor` には、次の2つのパブリックコンストラクターのいずれかが必要です。

* .ctor (IServiceProvider)
* .ctor ()

> [!NOTE]
> `IServiceProvider`コンストラクターに渡されるは null でもかまいません。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

インターフェイスは、 `IKeyEscrowSink` 機密情報のエスクローを実行できる型を表します。 シリアル化された記述子に機密情報 (暗号化マテリアルなど) が含まれている可能性があることを思い出してください。これは、 [IXmlEncryptor](#ixmlencryptor)型が最初に導入されたものです。 しかし、事故が発生し、キーリングが削除されたり、破損したりする可能性があります。

エスクローエスケープハッチを使用すると、構成済みの[IXmlEncryptor](#ixmlencryptor)によって変換される前に、未処理のシリアル化された XML にアクセスできます。 インターフェイスは、単一の API を公開します。

* ストア (Guid キー Id、XElement 要素)

`IKeyEscrowSink`ビジネスポリシーと一貫性のあるセキュリティで保護された方法で提供される要素を処理するのは、実装によって異なります。 可能な実装の1つとして、エスクローシンクが、証明書の秘密キーがエスクローされている既知の企業の x.509 証明書を使用して XML 要素を暗号化することが考えられます。`CertificateXmlEncryptor`この型は、このをサポートできます。 `IKeyEscrowSink`実装は、指定された要素を適切に永続化する役割も担います。

既定では、エスクローメカニズムは有効になっていませんが、サーバー管理者は[グローバルに構成](xref:security/data-protection/configuration/machine-wide-policy)することができます。 次のサンプルに示すように、メソッドを使用してプログラムで構成することもでき `IDataProtectionBuilder.AddKeyEscrowSink` ます。 `AddKeyEscrowSink` `IServiceCollection.AddSingleton` インスタンスはシングルトンとして使用されるため、メソッドオーバーロードはとのオーバーロードをミラー化し `IServiceCollection.AddInstance` `IKeyEscrowSink` ます。 複数のインスタンスが登録されている場合は `IKeyEscrowSink` 、キーの生成時にそれぞれが呼び出されます。そのため、キーを複数のメカニズムに同時にエスクローことができます。

インスタンスからマテリアルを読み取る API はありません `IKeyEscrowSink` 。 これは、エスクローメカニズムの設計理論と一貫性があります。これは、信頼された機関からキーマテリアルにアクセスできるようにするためのものであり、アプリケーション自体が信頼された機関ではないため、独自のエスクローマテリアルにアクセスできないようにする必要があります。

次のサンプルコードは、 `IKeyEscrowSink` "CONTOSODomain Admins" のメンバーだけが回復できるように、キーがエスクローされるを作成して登録する方法を示しています。

> [!NOTE]
> このサンプルを実行するには、ドメインに参加している Windows 8/Windows Server 2012 コンピューターで、ドメインコントローラーが Windows Server 2012 以降である必要があります。

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
