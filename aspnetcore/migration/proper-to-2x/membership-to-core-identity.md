---
title: ASP.NET メンバーシップ認証から ASP.NET Core 2.0 への移行Identity
author: isaac2004
description: メンバーシップ認証を使用して既存の ASP.NET アプリを ASP.NET Core 2.0 に移行する方法について説明し Identity ます。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: f039772f4276d0e8bcec2629350eba2ec0e7418c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399687"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>ASP.NET メンバーシップ認証から ASP.NET Core 2.0 への移行Identity

著者: [Isaac Levin](https://isaaclevin.com)

この記事では、メンバーシップ認証を使用して ASP.NET アプリのデータベーススキーマを ASP.NET Core 2.0 に移行する方法について説明し Identity ます。

> [!NOTE]
> このドキュメントでは、ASP.NET メンバーシップベースのアプリ用のデータベーススキーマを ASP.NET Core に使用されるデータベーススキーマに移行するために必要な手順について説明し Identity ます。 ASP.NET メンバーシップベースの認証から ASP.NET への移行の詳細について Identity は、「 [SQL メンバーシップからの既存の Identity アプリの ASP.NET への移行](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)」を参照してください。 ASP.NET Core の詳細について Identity は、「 [ Identity ASP.NET Core の概要](xref:security/authentication/identity)」を参照してください。

## <a name="review-of-membership-schema"></a>メンバーシップスキーマのレビュー

ASP.NET 2.0 より前の開発者は、アプリの認証と承認のプロセス全体を作成しようとしていました。 ASP.NET 2.0 では、メンバーシップが導入され、ASP.NET アプリ内のセキュリティを処理する定型的なソリューションが提供されています。 開発者は、 [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx)コマンドを使用して、スキーマを SQL Server データベースにブートストラップできるようになりました。 このコマンドを実行すると、次のテーブルがデータベースに作成されます。

  ![メンバーシップテーブル](identity/_static/membership-tables.png)

既存のアプリを ASP.NET Core 2.0 に移行するには、 Identity これらのテーブルのデータを新しいスキーマによって使用されるテーブルに移行する必要があり Identity ます。

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2.0 スキーマ

ASP.NET Core 2.0 は、 [Identity](/aspnet/identity/index) ASP.NET 4.5 で導入された原則に従います。 原則は共有されていますが、ASP.NET Core のバージョン間でもフレームワーク間の実装は異なります (「[認証の移行」および「 Identity ASP.NET Core 2.0 への移行](xref:migration/1x-to-2x/index)」を参照してください)。

ASP.NET Core 2.0 のスキーマを表示する最も簡単な方法 Identity は、新しい ASP.NET Core 2.0 アプリを作成することです。 Visual Studio 2017 で、次の手順を実行します。

1. **[File]**  >  **[New]**  >  **[Project]** の順に選択します。
1. *CoreIdentitySample*という名前の新しい**ASP.NET Core Web アプリケーション**プロジェクトを作成します。
1. ドロップダウンで**ASP.NET Core 2.0**を選択し、[ **Web アプリケーション**] を選択します。 このテンプレートは、 [ Razor Pages](xref:razor-pages/index)アプリを生成します。 [ **OK]** をクリックしてから、[**認証の変更**] をクリックします。
1. テンプレートに**個別のユーザーアカウント**を選択し Identity ます。 最後に、[ **ok**] をクリックし、[ **ok]** をクリックします。 Visual Studio によって、ASP.NET Core テンプレートを使用してプロジェクトが作成さ Identity れます。
1. [**ツール**] [  >  **NuGet パッケージマネージャー**] [  >  **パッケージマネージャーコンソール**] を選択して、[**パッケージマネージャーコンソール**(PMC)] ウィンドウを開きます。
1. PMC のプロジェクトルートに移動し、 [Entity Framework (EF) Core](/ef/core)コマンドを実行し `Update-Database` ます。

    ASP.NET Core 2.0 Identity では EF Core を使用して、認証データを格納するデータベースと対話します。 新しく作成したアプリを機能させるには、このデータを格納するデータベースが必要です。 新しいアプリを作成した後、データベース環境でスキーマを検査する最も簡単な方法は、EF Core の[移行](/ef/core/managing-schemas/migrations/)を使用してデータベースを作成することです。 このプロセスでは、ローカルまたはその他の場所にあるデータベースを作成し、そのスキーマを模倣します。 詳細については、前のドキュメントを参照してください。

    EF Core のコマンドは*appsettings.js*で指定されたデータベースの接続文字列を使用します。 次の接続文字列は、 *localhost*という名前のデータベースを*対象とし*ています。 この設定では、EF Core は接続文字列を使用するように構成されてい `DefaultConnection` ます。

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. [**ビュー**SQL Server オブジェクトエクスプローラー] を選択し  >  **SQL Server Object Explorer**ます。 appsettings.jsののプロパティで指定したデータベース名に対応するノードを展開 `ConnectionStrings:DefaultConnection` します。 *appsettings.json*

    この `Update-Database` コマンドは、スキーマで指定されたデータベースと、アプリの初期化に必要なデータを作成しました。 次の図は、前の手順で作成されたテーブル構造を示しています。

    ![Identityテーブル](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>スキーマを移行する

テーブルの構造とフィールドには、メンバーシップと ASP.NET Core の両方について微妙な違いがあり Identity ます。 このパターンは、ASP.NET および ASP.NET Core アプリでの認証/承認のために大幅に変更されています。 で引き続き使用される主なオブジェクト Identity は、*ユーザー*と*ロール*です。 ここでは、*ユーザー*、*ロール*、および*userroles*のマッピングテーブルについて説明します。

### <a name="users"></a>ユーザー

|*Identity<br>dbo.AspNetUsers*        ||*メンバーシップ <br> (dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**フィールド名**                 |**Type**|**フィールド名**                                    |**Type**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> すべてのフィールドマッピングは、メンバーシップから ASP.NET Core への一対一のリレーションシップに似ているとは限りません Identity 。 上の表は、既定のメンバーシップユーザースキーマを取得し、それを ASP.NET Core スキーマにマップして Identity います。 メンバーシップに使用されていたその他のカスタムフィールドは、手動でマップする必要があります。 このマッピングでは、パスワードの条件とパスワード salts の両方が2つの間で移行されないため、パスワードのマップはありません。 **パスワードを null として残し、ユーザーにパスワードのリセットを依頼することをお勧めします。** ASP.NET Core では Identity 、 `LockoutEnd` ユーザーがロックアウトされている場合は、将来の日付に設定する必要があります。これは移行スクリプトに示されています。

### <a name="roles"></a>役割

|*Identity<br>dbo.AspNetRoles)*        ||*メンバーシップ <br> (dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**フィールド名**                 |**Type**|**フィールド名**   |**Type**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>ユーザー ロール

|*Identity<br>dbo.AspNetUserRoles*||*メンバーシップ <br> (dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**フィールド名**           |**Type**  |**フィールド名**|**Type**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

*ユーザー*および*ロール*の移行スクリプトを作成する場合は、上記のマッピングテーブルを参照してください。 次の例では、データベースサーバーに2つのデータベースがあることを前提としています。 1つのデータベースには、既存の ASP.NET メンバーシップスキーマとデータが含まれています。 他の*CoreIdentitySample*データベースは、前に説明した手順を使用して作成されました。 詳細については、コメントがインラインで含まれています。

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

前のスクリプトが完了すると、前に作成した ASP.NET Core Identity アプリにメンバーシップユーザーが設定されます。 ユーザーはログインする前にパスワードを変更する必要があります。

> [!NOTE]
> メンバーシップシステムにユーザー名が電子メールアドレスと一致しないユーザーがいる場合は、これに対応するために以前に作成したアプリに変更を加える必要があります。 既定のテンプレートでは、 `UserName` とが `Email` 同じである必要があります。 これらが異なる場合は、の代わりにを使用するようにログインプロセスを変更する必要があり `UserName` `Email` ます。

`PageModel` *Pages\Account\Login.cshtml.cs*にあるログインページので、 `[EmailAddress]` *Email*プロパティから属性を削除します。 名前を*UserName*に変更します。 これ `EmailAddress` を行うには、*ビュー*と*PageModel*で説明されているすべての場所で変更が必要です。 結果は次のようになります。

 ![固定ログイン](identity/_static/fixed-login.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、SQL メンバーシップから ASP.NET Core 2.0 にユーザーを移植する方法について学習しました Identity 。 ASP.NET Core の詳細につい Identity ては、「」[を Identity ](xref:security/authentication/identity)参照してください。
