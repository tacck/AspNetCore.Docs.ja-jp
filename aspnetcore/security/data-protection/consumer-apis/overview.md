---
title: ASP.NET Core のコンシューマー Api の概要
author: rick-anderson
description: ASP.NET Core データ保護ライブラリ内で使用できるさまざまなコンシューマー Api の簡単な概要を説明します。
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 0bb163b677062c46a077731c014a40c602764594
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406720"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a><span data-ttu-id="709ef-103">ASP.NET Core のコンシューマー Api の概要</span><span class="sxs-lookup"><span data-stu-id="709ef-103">Consumer APIs overview for ASP.NET Core</span></span>

<span data-ttu-id="709ef-104">`IDataProtectionProvider`インターフェイスと `IDataProtector` インターフェイスは、コンシューマーがデータ保護システムを使用するための基本的なインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="709ef-104">The `IDataProtectionProvider` and `IDataProtector` interfaces are the basic interfaces through which consumers use the data protection system.</span></span> <span data-ttu-id="709ef-105">これらは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)パッケージに格納されています。</span><span class="sxs-lookup"><span data-stu-id="709ef-105">They're located in the [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.</span></span>

## <a name="idataprotectionprovider"></a><span data-ttu-id="709ef-106">IDataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="709ef-106">IDataProtectionProvider</span></span>

<span data-ttu-id="709ef-107">プロバイダーインターフェイスは、データ保護システムのルートを表します。</span><span class="sxs-lookup"><span data-stu-id="709ef-107">The provider interface represents the root of the data protection system.</span></span> <span data-ttu-id="709ef-108">データの保護または保護解除に直接使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="709ef-108">It cannot directly be used to protect or unprotect data.</span></span> <span data-ttu-id="709ef-109">代わりに、コンシューマーはを呼び出すことによってへの参照を取得する必要があり `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` ます。ここで、目的は、目的のコンシューマーユースケースを説明する文字列です。</span><span class="sxs-lookup"><span data-stu-id="709ef-109">Instead, the consumer must get a reference to an `IDataProtector` by calling `IDataProtectionProvider.CreateProtector(purpose)`, where purpose is a string that describes the intended consumer use case.</span></span> <span data-ttu-id="709ef-110">このパラメーターの目的と適切な値を選択する方法の詳細については、「[目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="709ef-110">See [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings) for much more information on the intent of this parameter and how to choose an appropriate value.</span></span>

## <a name="idataprotector"></a><span data-ttu-id="709ef-111">IDataProtector</span><span class="sxs-lookup"><span data-stu-id="709ef-111">IDataProtector</span></span>

<span data-ttu-id="709ef-112">プロテクターインターフェイスは、の呼び出しによって返され `CreateProtector` ます。コンシューマーはこのインターフェイスを使用して、保護および保護解除操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="709ef-112">The protector interface is returned by a call to `CreateProtector`, and it's this interface which consumers can use to perform protect and unprotect operations.</span></span>

<span data-ttu-id="709ef-113">データを保護するには、データをメソッドに渡し `Protect` ます。</span><span class="sxs-lookup"><span data-stu-id="709ef-113">To protect a piece of data, pass the data to the `Protect` method.</span></span> <span data-ttu-id="709ef-114">基本インターフェイスは byte []-> byte [] を変換するメソッドを定義しますが、文字列 > 文字列を変換するオーバーロード (拡張メソッドとして提供される) もあります。</span><span class="sxs-lookup"><span data-stu-id="709ef-114">The basic interface defines a method which converts byte[] -> byte[], but there's also an overload (provided as an extension method) which converts string -> string.</span></span> <span data-ttu-id="709ef-115">2つのメソッドによって提供されるセキュリティは同じです。開発者は、ユースケースに最も便利なオーバーロードのいずれかを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="709ef-115">The security offered by the two methods is identical; the developer should choose whichever overload is most convenient for their use case.</span></span> <span data-ttu-id="709ef-116">選択されたオーバーロードに関係なく、Protect メソッドによって返される値は保護されるようになりました (読んだりと校正)。アプリケーションは信頼されていないクライアントに送信できます。</span><span class="sxs-lookup"><span data-stu-id="709ef-116">Irrespective of the overload chosen, the value returned by the Protect method is now protected (enciphered and tamper-proofed), and the application can send it to an untrusted client.</span></span>

<span data-ttu-id="709ef-117">以前に保護されていたデータの一部を保護解除するには、保護されたデータをメソッドに渡し `Unprotect` ます。</span><span class="sxs-lookup"><span data-stu-id="709ef-117">To unprotect a previously-protected piece of data, pass the protected data to the `Unprotect` method.</span></span> <span data-ttu-id="709ef-118">(開発者の便宜のために、byte [] ベースのオーバーロードと文字列ベースのオーバーロードがあります)。このに対するの以前の呼び出しによって保護されたペイロードが生成された場合 `Protect` `IDataProtector` 、メソッドは、保護されていない `Unprotect` 元のペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="709ef-118">(There are byte[]-based and string-based overloads for developer convenience.) If the protected payload was generated by an earlier call to `Protect` on this same `IDataProtector`, the `Unprotect` method will return the original unprotected payload.</span></span> <span data-ttu-id="709ef-119">保護されたペイロードが改ざんされた場合、または別のによって生成された場合、 `IDataProtector` `Unprotect` メソッドは system.security.cryptography.cryptographicexception> をスローします。</span><span class="sxs-lookup"><span data-stu-id="709ef-119">If the protected payload has been tampered with or was produced by a different `IDataProtector`, the `Unprotect` method will throw CryptographicException.</span></span>

<span data-ttu-id="709ef-120">同一の概念は、 `IDataProtector` 目的の概念に戻ります。</span><span class="sxs-lookup"><span data-stu-id="709ef-120">The concept of same vs. different `IDataProtector` ties back to the concept of purpose.</span></span> <span data-ttu-id="709ef-121">2つの `IDataProtector` インスタンスが同じルートから生成され、の呼び出しで別の目的の文字列を使用して生成された場合 `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` 、それらは[異なるプロテクター](xref:security/data-protection/consumer-apis/purpose-strings)と見なされ、もう一方のインスタンスによって生成されたペイロードの保護を解除することはできません。</span><span class="sxs-lookup"><span data-stu-id="709ef-121">If two `IDataProtector` instances were generated from the same root `IDataProtectionProvider` but via different purpose strings in the call to `IDataProtectionProvider.CreateProtector`, then they're considered [different protectors](xref:security/data-protection/consumer-apis/purpose-strings), and one won't be able to unprotect payloads generated by the other.</span></span>

## <a name="consuming-these-interfaces"></a><span data-ttu-id="709ef-122">これらのインターフェイスの使用</span><span class="sxs-lookup"><span data-stu-id="709ef-122">Consuming these interfaces</span></span>

<span data-ttu-id="709ef-123">DI 対応のコンポーネントの場合、使用目的は、コンポーネントが `IDataProtectionProvider` コンストラクター内でパラメーターを受け取り、コンポーネントがインスタンス化されるときに DI システムが自動的にこのサービスを提供することです。</span><span class="sxs-lookup"><span data-stu-id="709ef-123">For a DI-aware component, the intended usage is that the component takes an `IDataProtectionProvider` parameter in its constructor and that the DI system automatically provides this service when the component is instantiated.</span></span>

> [!NOTE]
> <span data-ttu-id="709ef-124">一部のアプリケーション (コンソールアプリケーションや ASP.NET 4.x アプリケーションなど) が DI に対応していない可能性があるため、ここで説明するメカニズムを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="709ef-124">Some applications (such as console applications or ASP.NET 4.x applications) might not be DI-aware so cannot use the mechanism described here.</span></span> <span data-ttu-id="709ef-125">これらのシナリオでは、di を介さずにプロバイダーのインスタンスを取得する方法の詳細については、 [Di 非対応のシナリオ](xref:security/data-protection/configuration/non-di-scenarios)に関するドキュメントを参照してください `IDataProtection` 。</span><span class="sxs-lookup"><span data-stu-id="709ef-125">For these scenarios consult the [Non DI Aware Scenarios](xref:security/data-protection/configuration/non-di-scenarios) document for more information on getting an instance of an `IDataProtection` provider without going through DI.</span></span>

<span data-ttu-id="709ef-126">次の例では、3つの概念を示します。</span><span class="sxs-lookup"><span data-stu-id="709ef-126">The following sample demonstrates three concepts:</span></span>

1. <span data-ttu-id="709ef-127">[データ保護システム](xref:security/data-protection/configuration/overview)をサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="709ef-127">[Add the data protection system](xref:security/data-protection/configuration/overview) to the service container,</span></span>

2. <span data-ttu-id="709ef-128">DI を使用して、のインスタンスを受信する `IDataProtectionProvider`</span><span class="sxs-lookup"><span data-stu-id="709ef-128">Using DI to receive an instance of an `IDataProtectionProvider`, and</span></span>

3. <span data-ttu-id="709ef-129">からを作成 `IDataProtector` `IDataProtectionProvider` し、それを使用してデータを保護および保護解除します。</span><span class="sxs-lookup"><span data-stu-id="709ef-129">Creating an `IDataProtector` from an `IDataProtectionProvider` and using it to protect and unprotect data.</span></span>

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="709ef-130">パッケージ AspNetCore には、 `IServiceProvider.GetDataProtector` 開発者の便宜として拡張メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="709ef-130">The package Microsoft.AspNetCore.DataProtection.Abstractions contains an extension method `IServiceProvider.GetDataProtector` as a developer convenience.</span></span> <span data-ttu-id="709ef-131">サービスプロバイダーからを取得し、を呼び出すことで、単一の操作としてカプセル化し `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` ます。</span><span class="sxs-lookup"><span data-stu-id="709ef-131">It encapsulates as a single operation both retrieving an `IDataProtectionProvider` from the service provider and calling `IDataProtectionProvider.CreateProtector`.</span></span> <span data-ttu-id="709ef-132">次の例では、その使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="709ef-132">The following sample demonstrates its usage.</span></span>

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> <span data-ttu-id="709ef-133">との `IDataProtectionProvider` インスタンス `IDataProtector` は、複数の呼び出し元に対してスレッドセーフです。</span><span class="sxs-lookup"><span data-stu-id="709ef-133">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="709ef-134">コンポーネントがへの呼び出しによってへの参照を取得すると、その `IDataProtector` `CreateProtector` 参照がとの複数の呼び出しに使用されることを意図して `Protect` `Unprotect` います。</span><span class="sxs-lookup"><span data-stu-id="709ef-134">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span> <span data-ttu-id="709ef-135">保護された `Unprotect` ペイロードを検証または解読できない場合、の呼び出しは system.security.cryptography.cryptographicexception> をスローします。</span><span class="sxs-lookup"><span data-stu-id="709ef-135">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="709ef-136">コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証クッキーを読み取るコンポーネントは、このエラーを処理し、要求を完全に失敗させるのではなく、cookie がまったくないかのように要求を処理することがあります。</span><span class="sxs-lookup"><span data-stu-id="709ef-136">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="709ef-137">この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="709ef-137">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
