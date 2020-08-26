---
title: ASP.NET メンバーシップ認証から ASP.NET Core 2.0 への移行 Identity
author: isaac2004
description: メンバーシップ認証を使用して既存の ASP.NET アプリを ASP.NET Core 2.0 に移行する方法について説明し Identity ます。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: a9ec02381b156a6599042d8e504a476036246302
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865557"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="469c3-103">ASP.NET メンバーシップ認証から ASP.NET Core 2.0 への移行 Identity</span><span class="sxs-lookup"><span data-stu-id="469c3-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="469c3-104">著者: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="469c3-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="469c3-105">この記事では、メンバーシップ認証を使用して ASP.NET アプリのデータベーススキーマを ASP.NET Core 2.0 に移行する方法について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="469c3-106">このドキュメントでは、ASP.NET メンバーシップベースのアプリ用のデータベーススキーマを、に使用されるデータベーススキーマに移行するために必要な手順について説明し ASP.NET Core Identity ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="469c3-107">ASP.NET メンバーシップベースの認証から ASP.NET への移行の詳細について Identity は、「 [SQL メンバーシップからの既存の Identity アプリの ASP.NET への移行](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="469c3-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="469c3-108">の詳細について ASP.NET Core Identity は、「 [ Identity ASP.NET Core の概要](xref:security/authentication/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="469c3-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="469c3-109">メンバーシップスキーマのレビュー</span><span class="sxs-lookup"><span data-stu-id="469c3-109">Review of Membership schema</span></span>

<span data-ttu-id="469c3-110">ASP.NET 2.0 より前の開発者は、アプリの認証と承認のプロセス全体を作成しようとしていました。</span><span class="sxs-lookup"><span data-stu-id="469c3-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="469c3-111">ASP.NET 2.0 では、メンバーシップが導入され、ASP.NET アプリ内のセキュリティを処理する定型的なソリューションが提供されています。</span><span class="sxs-lookup"><span data-stu-id="469c3-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="469c3-112">開発者は、コマンドを使用して、スキーマを SQL Server データベースにブートストラップできるようになりました <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> 。</span><span class="sxs-lookup"><span data-stu-id="469c3-112">Developers were now able to bootstrap a schema into a SQL Server database with the <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> command.</span></span> <span data-ttu-id="469c3-113">このコマンドを実行すると、次のテーブルがデータベースに作成されます。</span><span class="sxs-lookup"><span data-stu-id="469c3-113">After running this command, the following tables were created in the database.</span></span>

  ![メンバーシップテーブル](identity/_static/membership-tables.png)

<span data-ttu-id="469c3-115">既存のアプリを ASP.NET Core 2.0 に移行するには、 Identity これらのテーブルのデータを新しいスキーマによって使用されるテーブルに移行する必要があり Identity ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="469c3-116">ASP.NET Core Identity 2.0 スキーマ</span><span class="sxs-lookup"><span data-stu-id="469c3-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="469c3-117">ASP.NET Core 2.0 は、 [Identity](/aspnet/identity/index) ASP.NET 4.5 で導入された原則に従います。</span><span class="sxs-lookup"><span data-stu-id="469c3-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="469c3-118">原則は共有されていますが、ASP.NET Core のバージョン間でもフレームワーク間の実装は異なります (「 [認証の移行」および「 Identity ASP.NET Core 2.0 への移行](xref:migration/1x-to-2x/index)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="469c3-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="469c3-119">ASP.NET Core 2.0 のスキーマを表示する最も簡単な方法 Identity は、新しい ASP.NET Core 2.0 アプリを作成することです。</span><span class="sxs-lookup"><span data-stu-id="469c3-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="469c3-120">Visual Studio 2017 で、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="469c3-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="469c3-121">**[File]**  >  **[New]**  >  **[Project]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="469c3-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="469c3-122">*Core Identity Sample*という名前の新しい**ASP.NET Core Web アプリケーション**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="469c3-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="469c3-123">ドロップダウンで **ASP.NET Core 2.0** を選択し、[ **Web アプリケーション**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="469c3-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="469c3-124">このテンプレートは、 [ Razor Pages](xref:razor-pages/index)アプリを生成します。</span><span class="sxs-lookup"><span data-stu-id="469c3-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="469c3-125">[ **OK]** をクリックしてから、[ **認証の変更**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="469c3-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="469c3-126">テンプレートに **個別のユーザーアカウント** を選択し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="469c3-127">最後に、[ **ok**] をクリックし、[ **ok]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="469c3-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="469c3-128">Visual Studio によって、テンプレートを使用してプロジェクトが作成さ ASP.NET Core Identity れます。</span><span class="sxs-lookup"><span data-stu-id="469c3-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="469c3-129">[**ツール**] [  >  **NuGet パッケージマネージャー**] [  >  **パッケージマネージャーコンソール**] を選択して、[**パッケージマネージャーコンソール**(PMC)] ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="469c3-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="469c3-130">PMC のプロジェクトルートに移動し、 [Entity Framework (EF) Core](/ef/core)コマンドを実行し `Update-Database` ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="469c3-131">ASP.NET Core 2.0 Identity では EF Core を使用して、認証データを格納するデータベースと対話します。</span><span class="sxs-lookup"><span data-stu-id="469c3-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="469c3-132">新しく作成したアプリを機能させるには、このデータを格納するデータベースが必要です。</span><span class="sxs-lookup"><span data-stu-id="469c3-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="469c3-133">新しいアプリを作成した後、データベース環境でスキーマを検査する最も簡単な方法は、EF Core の [移行](/ef/core/managing-schemas/migrations/)を使用してデータベースを作成することです。</span><span class="sxs-lookup"><span data-stu-id="469c3-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="469c3-134">このプロセスでは、ローカルまたはその他の場所にあるデータベースを作成し、そのスキーマを模倣します。</span><span class="sxs-lookup"><span data-stu-id="469c3-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="469c3-135">詳細については、前のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="469c3-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="469c3-136">EF Core のコマンドは *appsettings.js*で指定されたデータベースの接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="469c3-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="469c3-137">次の接続文字列は、 *localhost* という名前のデータベースを *対象とし*ています。</span><span class="sxs-lookup"><span data-stu-id="469c3-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="469c3-138">この設定では、EF Core は接続文字列を使用するように構成されてい `DefaultConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="469c3-139">[**ビュー**SQL Server オブジェクトエクスプローラー] を選択し  >  **SQL Server Object Explorer**ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="469c3-140">appsettings.jsののプロパティで指定したデータベース名に対応するノードを展開 `ConnectionStrings:DefaultConnection` します。 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="469c3-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="469c3-141">この `Update-Database` コマンドは、スキーマで指定されたデータベースと、アプリの初期化に必要なデータを作成しました。</span><span class="sxs-lookup"><span data-stu-id="469c3-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="469c3-142">次の図は、前の手順で作成されたテーブル構造を示しています。</span><span class="sxs-lookup"><span data-stu-id="469c3-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: no-loc (Identity)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="469c3-144">スキーマを移行する</span><span class="sxs-lookup"><span data-stu-id="469c3-144">Migrate the schema</span></span>

<span data-ttu-id="469c3-145">テーブルの構造とフィールドには、メンバーシップとの両方について微妙な違いがあり ASP.NET Core Identity ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="469c3-146">このパターンは、ASP.NET および ASP.NET Core アプリでの認証/承認のために大幅に変更されています。</span><span class="sxs-lookup"><span data-stu-id="469c3-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="469c3-147">で引き続き使用される主なオブジェクト Identity は、 *ユーザー* と *ロール*です。</span><span class="sxs-lookup"><span data-stu-id="469c3-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="469c3-148">ここでは、 *ユーザー*、 *ロール*、および *userroles*のマッピングテーブルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="469c3-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="469c3-149">ユーザー</span><span class="sxs-lookup"><span data-stu-id="469c3-149">Users</span></span>

|Identity<br><span data-ttu-id="469c3-150">( `dbo.AspNetUsers` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="469c3-151">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-151">Type</span></span>     |<span data-ttu-id="469c3-152">Membership</span><span class="sxs-lookup"><span data-stu-id="469c3-152">Membership</span></span><br><span data-ttu-id="469c3-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="469c3-154">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="469c3-155">すべてのフィールドマッピングが、メンバーシップからの一対一のリレーションシップに似ているとは限りません ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="469c3-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="469c3-156">上の表は、既定のメンバーシップユーザースキーマを取得し、スキーマにマップして ASP.NET Core Identity います。</span><span class="sxs-lookup"><span data-stu-id="469c3-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="469c3-157">メンバーシップに使用されていたその他のカスタムフィールドは、手動でマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="469c3-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="469c3-158">このマッピングでは、パスワードの条件とパスワード salts の両方が2つの間で移行されないため、パスワードのマップはありません。</span><span class="sxs-lookup"><span data-stu-id="469c3-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="469c3-159">**パスワードを null として残し、ユーザーにパスワードのリセットを依頼することをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="469c3-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="469c3-160">では ASP.NET Core Identity 、 `LockoutEnd` ユーザーがロックアウトされている場合は、将来の日付に設定する必要があります。これは移行スクリプトに示されています。</span><span class="sxs-lookup"><span data-stu-id="469c3-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="469c3-161">役割</span><span class="sxs-lookup"><span data-stu-id="469c3-161">Roles</span></span>

|Identity<br><span data-ttu-id="469c3-162">( `dbo.AspNetRoles` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="469c3-163">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-163">Type</span></span>|<span data-ttu-id="469c3-164">Membership</span><span class="sxs-lookup"><span data-stu-id="469c3-164">Membership</span></span><br><span data-ttu-id="469c3-165">( `dbo.aspnet_Roles` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="469c3-166">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="469c3-167">ユーザー ロール</span><span class="sxs-lookup"><span data-stu-id="469c3-167">User Roles</span></span>

|Identity<br><span data-ttu-id="469c3-168">( `dbo.AspNetUserRoles` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="469c3-169">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-169">Type</span></span>|<span data-ttu-id="469c3-170">Membership</span><span class="sxs-lookup"><span data-stu-id="469c3-170">Membership</span></span><br><span data-ttu-id="469c3-171">( `dbo.aspnet_UsersInRoles` ) 列</span><span class="sxs-lookup"><span data-stu-id="469c3-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="469c3-172">種類</span><span class="sxs-lookup"><span data-stu-id="469c3-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="469c3-173">*ユーザー*および*ロール*の移行スクリプトを作成する場合は、上記のマッピングテーブルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="469c3-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="469c3-174">次の例では、データベースサーバーに2つのデータベースがあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="469c3-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="469c3-175">1つのデータベースには、既存の ASP.NET メンバーシップスキーマとデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="469c3-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="469c3-176">もう1つの *コア Identity サンプル* データベースは、前に説明した手順を使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="469c3-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="469c3-177">詳細については、コメントがインラインで含まれています。</span><span class="sxs-lookup"><span data-stu-id="469c3-177">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="469c3-178">前のスクリプトが完了すると、 ASP.NET Core Identity 前に作成したアプリにメンバーシップユーザーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="469c3-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="469c3-179">ユーザーはログインする前にパスワードを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="469c3-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="469c3-180">メンバーシップシステムにユーザー名が電子メールアドレスと一致しないユーザーがいる場合は、これに対応するために以前に作成したアプリに変更を加える必要があります。</span><span class="sxs-lookup"><span data-stu-id="469c3-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="469c3-181">既定のテンプレートでは、 `UserName` とが `Email` 同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="469c3-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="469c3-182">これらが異なる場合は、の代わりにを使用するようにログインプロセスを変更する必要があり `UserName` `Email` ます。</span><span class="sxs-lookup"><span data-stu-id="469c3-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="469c3-183">`PageModel` *Pages\Account\Login.cshtml.cs*にあるログインページので、 `[EmailAddress]` *Email*プロパティから属性を削除します。</span><span class="sxs-lookup"><span data-stu-id="469c3-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="469c3-184">名前を *UserName*に変更します。</span><span class="sxs-lookup"><span data-stu-id="469c3-184">Rename it to *UserName*.</span></span> <span data-ttu-id="469c3-185">これ `EmailAddress` を行うには、 *ビュー* と *PageModel*で説明されているすべての場所で変更が必要です。</span><span class="sxs-lookup"><span data-stu-id="469c3-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="469c3-186">結果は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="469c3-186">The result looks like the following:</span></span>

 ![固定ログイン](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="469c3-188">次の手順</span><span class="sxs-lookup"><span data-stu-id="469c3-188">Next steps</span></span>

<span data-ttu-id="469c3-189">このチュートリアルでは、SQL メンバーシップから ASP.NET Core 2.0 にユーザーを移植する方法について学習しました Identity 。</span><span class="sxs-lookup"><span data-stu-id="469c3-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="469c3-190">の詳細につい ASP.NET Core Identity ては、「の[概要 Identity ](xref:security/authentication/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="469c3-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
