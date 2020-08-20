---
title: ASP.NET Core のキー格納形式
author: rick-anderson
description: ASP.NET Core データ保護のキーストレージ形式の実装の詳細について説明します。
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631993"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="0fbfc-103">ASP.NET Core のキー格納形式</span><span class="sxs-lookup"><span data-stu-id="0fbfc-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="0fbfc-104">オブジェクトは、XML 形式で保存されます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="0fbfc-105">キーストレージの既定のディレクトリは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="0fbfc-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="0fbfc-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="0fbfc-107">macOS/Linux: *$HOME/.aspnet/dataprotection-keys*</span><span class="sxs-lookup"><span data-stu-id="0fbfc-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="0fbfc-108">\<key> 要素</span><span class="sxs-lookup"><span data-stu-id="0fbfc-108">The \<key> element</span></span>

<span data-ttu-id="0fbfc-109">キーは、キーリポジトリの最上位レベルのオブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="0fbfc-110">規則によるキーのファイル名は **キー {guid} .xml です**。ここで、{guid} はキーの id です。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="0fbfc-111">このようなファイルには1つのキーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-111">Each such file contains a single key.</span></span> <span data-ttu-id="0fbfc-112">ファイルの形式は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="0fbfc-113">要素には \<key> 、次の属性と子要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="0fbfc-114">キー id。この値は権限のあるものとして扱われます。ファイル名は、人間が読みやすくするための単なる言えです。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="0fbfc-115">\<key>現在1で修正されている要素のバージョン。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="0fbfc-116">キーの作成、アクティブ化、および有効期限。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="0fbfc-117">\<descriptor>このキーに含まれている認証済み暗号化の実装に関する情報を格納している要素。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="0fbfc-118">上の例では、キーの id は {80732141-ec8f-4b80-af9c-c4d2d1ff8901} であり、2015年3月19日に作成され、アクティブ化され、90日の有効期間があります。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="0fbfc-119">(場合によっては、ライセンス認証日が、この例のように作成日よりも少し前になることがあります。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="0fbfc-120">これは、Api が動作し、実際には無害であることが原因です)。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="0fbfc-121">\<descriptor> 要素</span><span class="sxs-lookup"><span data-stu-id="0fbfc-121">The \<descriptor> element</span></span>

<span data-ttu-id="0fbfc-122">Outer 要素には \<descriptor> 、deserializerType 属性が含まれています。これは、i認証 Ated Tor記述子デシリアライザーを実装する型のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="0fbfc-123">この型は、内部要素を読み取り、内に格納されて \<descriptor> いる情報を解析します。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="0fbfc-124">要素の特定の形式は、 \<descriptor> キーによってカプセル化される認証済みの暗号化機能の実装によって異なります。また、各デシリアライザー型では、この形式が若干異なることが想定されます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="0fbfc-125">ただし、一般に、この要素にはアルゴリズム情報 (名前、型、Oid、または類似) とシークレットキーマテリアルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="0fbfc-126">上の例では、記述子は、このキーが AES-256-CBC encryption + HMACSHA256 検証をラップすることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="0fbfc-127">\<encryptedSecret> 要素</span><span class="sxs-lookup"><span data-stu-id="0fbfc-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="0fbfc-128">暗号化された形式の秘密キーマテリアルを含む\*\* &lt; encryptedsecret &gt; \*\*要素は、保存[時のシークレットの暗号化が有効になっ](xref:security/data-protection/implementation/key-encryption-at-rest)ている場合に存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="0fbfc-129">属性 `decryptorType` は、 [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)を実装する型のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="0fbfc-130">この型は、内部の\*\* &lt; encryptedKey &gt; \*\*要素を読み取り、復号化して元のプレーンテキストを回復する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="0fbfc-131">と同様に、 `<descriptor>` 要素の特定の形式は、使用されて `<encryptedSecret>` いる保存時の暗号化メカニズムに依存します。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="0fbfc-132">上の例では、コメントごとに Windows DPAPI を使用してマスターキーが暗号化されています。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="0fbfc-133">\<revocation> 要素</span><span class="sxs-lookup"><span data-stu-id="0fbfc-133">The \<revocation> element</span></span>

<span data-ttu-id="0fbfc-134">失効は、キーリポジトリの最上位レベルのオブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="0fbfc-135">慣例により、失効はファイル名の **失効-{timestamp} .xml** (特定の日付より前のすべてのキーを取り消す場合) または **失効-{guid} .xml** (特定のキーを取り消す場合) を持ちます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="0fbfc-136">各ファイルには1つの要素が含まれてい \<revocation> ます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="0fbfc-137">個々のキーの失効の場合、ファイルの内容は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="0fbfc-138">この場合、指定されたキーのみが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="0fbfc-139">ただし、キー id が "\*" の場合、次の例のように、作成日が指定した失効日より前のすべてのキーが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="0fbfc-140">\<reason>要素がシステムによって読み取られることはありません。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="0fbfc-141">これは、ユーザーが判読できる失効の理由を格納するための便利な場所です。</span><span class="sxs-lookup"><span data-stu-id="0fbfc-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
