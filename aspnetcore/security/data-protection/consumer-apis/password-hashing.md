---
title: ASP.NET Core でのパスワードのハッシュ
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用してパスワードをハッシュする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051864"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="ba6d3-103">ASP.NET Core でのパスワードのハッシュ</span><span class="sxs-lookup"><span data-stu-id="ba6d3-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="ba6d3-104">データ保護コードベースには、暗号化キー派生関数を含むパッケージ *AspNetCore* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="ba6d3-105">このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="ba6d3-106">完全に独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-106">It can be used completely independently.</span></span> <span data-ttu-id="ba6d3-107">ソースは、便宜上、データ保護コードベースと共に存在します。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="ba6d3-108">パッケージには、現在、 `KeyDerivation.Pbkdf2` [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="ba6d3-109">この API は .NET Framework の既存の [Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="ba6d3-110">この `KeyDerivation.Pbkdf2` メソッドは、複数の PRFs (現在、、および) の使用をサポートしていますが、 `HMACSHA1` `HMACSHA256` `HMACSHA512` `Rfc2898DeriveBytes` 型はのみをサポートしてい `HMACSHA1` ます。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="ba6d3-111">メソッドは、 `KeyDerivation.Pbkdf2` 現在のオペレーティングシステムを検出し、ルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="ba6d3-112">(Windows 8 では、のスループットの約10倍が提供さ `Rfc2898DeriveBytes` れます)。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="ba6d3-113">メソッドでは、 `KeyDerivation.Pbkdf2` 呼び出し元がすべてのパラメーター (salt、PRF、およびイテレーション数) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="ba6d3-114">型は、 `Rfc2898DeriveBytes` これらの既定値を提供します。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="ba6d3-115">実際の[source code](https://github.com/dotnet/AspNetCore/blob/master/src/:::no-loc(Identity):::/Extensions.Core/src/PasswordHasher.cs) :::no-loc(ASP.NET Core Identity)::: `PasswordHasher` ユースケースについては、のソースコードを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba6d3-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/:::no-loc(Identity):::/Extensions.Core/src/PasswordHasher.cs) for :::no-loc(ASP.NET Core Identity):::'s `PasswordHasher` type for a real-world use case.</span></span>
