---
title: ASP.NET Core でのパスワードのハッシュ
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用してパスワードをハッシュする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 643d468763c6a935fc618a22920cb79119258087
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018391"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core でのパスワードのハッシュ

データ保護コードベースには、暗号化キー派生関数を含むパッケージ*AspNetCore*が含まれています。 このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。 完全に独立して使用できます。 ソースは、便宜上、データ保護コードベースと共に存在します。

パッケージには、現在、 `KeyDerivation.Pbkdf2` [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッドが用意されています。 この API は .NET Framework の既存の[Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。

1. この `KeyDerivation.Pbkdf2` メソッドは、複数の PRFs (現在、、および) の使用をサポートしていますが、 `HMACSHA1` `HMACSHA256` `HMACSHA512` `Rfc2898DeriveBytes` 型はのみをサポートしてい `HMACSHA1` ます。

2. メソッドは、 `KeyDerivation.Pbkdf2` 現在のオペレーティングシステムを検出し、ルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。 (Windows 8 では、のスループットの約10倍が提供さ `Rfc2898DeriveBytes` れます)。

3. メソッドでは、 `KeyDerivation.Pbkdf2` 呼び出し元がすべてのパラメーター (salt、PRF、およびイテレーション数) を指定する必要があります。 型は、 `Rfc2898DeriveBytes` これらの既定値を提供します。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

実際の[source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) Identity `PasswordHasher` ユースケースについては、ASP.NET Core の型のソースコードを参照してください。
