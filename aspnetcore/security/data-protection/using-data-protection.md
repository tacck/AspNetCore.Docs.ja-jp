---
title: ASP.NET Core でのデータ保護 Api の概要
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用して、アプリのデータを保護および復号化する方法について説明します。
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051656"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="e360d-103">ASP.NET Core でのデータ保護 Api の概要</span><span class="sxs-lookup"><span data-stu-id="e360d-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="e360d-104">データの保護は、次の手順で最も簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e360d-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="e360d-105">データ保護プロバイダーからデータプロテクターを作成します。</span><span class="sxs-lookup"><span data-stu-id="e360d-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="e360d-106">`Protect`保護するデータを指定してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e360d-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="e360d-107">`Unprotect`プレーンテキストに戻すデータを使用して、メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e360d-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="e360d-108">ASP.NET Core やなど、ほとんどのフレームワークとアプリケーションモデルでは、 :::no-loc(SignalR)::: データ保護システムが既に構成されており、依存関係の挿入によってアクセスするサービスコンテナーに追加されています。</span><span class="sxs-lookup"><span data-stu-id="e360d-108">Most frameworks and app models, such as ASP.NET Core or :::no-loc(SignalR):::, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="e360d-109">次のサンプルでは、依存関係の挿入のためのサービスコンテナーの構成、データ保護スタックの登録、DI を使用したデータ保護プロバイダーの受信、保護機能の作成、データの復号化の保護を行う方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e360d-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="e360d-110">保護機能を作成するときは、1つまたは複数の [目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e360d-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="e360d-111">目的の文字列は、コンシューマー間の分離を提供します。</span><span class="sxs-lookup"><span data-stu-id="e360d-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="e360d-112">たとえば、目的の文字列が "green" で作成されたプロテクターは、"紫" の目的で保護機能によって提供されるデータの保護を解除することはできません。</span><span class="sxs-lookup"><span data-stu-id="e360d-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="e360d-113">との `IDataProtectionProvider` インスタンス `IDataProtector` は、複数の呼び出し元に対してスレッドセーフです。</span><span class="sxs-lookup"><span data-stu-id="e360d-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="e360d-114">コンポーネントがへの呼び出しによってへの参照を取得すると、その `IDataProtector` `CreateProtector` 参照がとの複数の呼び出しに使用されることを意図して `Protect` `Unprotect` います。</span><span class="sxs-lookup"><span data-stu-id="e360d-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="e360d-115">保護された `Unprotect` ペイロードを検証または解読できない場合、の呼び出しは system.security.cryptography.cryptographicexception> をスローします。</span><span class="sxs-lookup"><span data-stu-id="e360d-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="e360d-116">コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証を読み取るコンポーネントは、 :::no-loc(cookie)::: このエラーを処理し、要求を :::no-loc(cookie)::: 完全に失敗させるのではなく、まったく存在しないかのように要求を処理することがあります。</span><span class="sxs-lookup"><span data-stu-id="e360d-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication :::no-loc(cookie):::s might handle this error and treat the request as if it had no :::no-loc(cookie)::: at all rather than fail the request outright.</span></span> <span data-ttu-id="e360d-117">この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e360d-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
