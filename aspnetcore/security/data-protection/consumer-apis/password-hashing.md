---
title: ASP.NET Core でのパスワードのハッシュ
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用してパスワードをハッシュする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769781"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core でのパスワードのハッシュ

データ保護コードベースには、暗号化キー派生関数を含むパッケージ*AspNetCore*が含まれています。 このパッケージはスタンドアロンコンポーネントであり、データ保護システムの残りの部分には依存関係がありません。 完全に独立して使用できます。 ソースは、便宜上、データ保護コードベースと共に存在します。

パッケージには、現在、 `KeyDerivation.Pbkdf2` [PBKDF2 アルゴリズム](https://tools.ietf.org/html/rfc2898#section-5.2)を使用してパスワードをハッシュできるメソッドが用意されています。 この API は .NET Framework の既存の[Rfc2898DeriveBytes 型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)と非常によく似ていますが、次の3つの重要な違いがあります。

1. この`KeyDerivation.Pbkdf2`メソッドは、複数の prfs ( `HMACSHA1`現在`HMACSHA256`、、 `HMACSHA512`および) の使用`Rfc2898DeriveBytes`をサポートし`HMACSHA1`ていますが、型はのみをサポートしています。

2. メソッド`KeyDerivation.Pbkdf2`は、現在のオペレーティングシステムを検出し、ルーチンの最も最適化された実装を選択しようとします。これにより、特定の場合にパフォーマンスが格段に向上します。 (Windows 8 では、の`Rfc2898DeriveBytes`スループットの約10倍が提供されます)。

3. メソッド`KeyDerivation.Pbkdf2`では、呼び出し元がすべてのパラメーター (SALT、PRF、およびイテレーション数) を指定する必要があります。 型`Rfc2898DeriveBytes`は、これらの既定値を提供します。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

実際のユースケースについIdentityて`PasswordHasher`は、ASP.NET Core の型の[ソースコード](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)を参照してください。
