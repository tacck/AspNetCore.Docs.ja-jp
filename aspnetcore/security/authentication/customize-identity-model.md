---
title: IdentityASP.NET Core でのモデルのカスタマイズ
author: ajcvickers
description: この記事では、ASP.NET Core の基になる Entity Framework Core データモデルをカスタマイズする方法について説明し Identity ます。
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 3a5bac0e3e34602b1f8a85a7bcde1ba92b372607
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399167"
---
# <a name="identity-model-customization-in-aspnet-core"></a>IdentityASP.NET Core でのモデルのカスタマイズ

[Arthur ヴィッカース](https://github.com/ajcvickers)

ASP.NET Core Identity は ASP.NET Core アプリでユーザーアカウントを管理および格納するためのフレームワークを提供します。 Identityは、認証メカニズムとして**個々のユーザーアカウント**が選択されたときにプロジェクトに追加されます。 既定では、は Identity Entity Framework (EF) コアデータモデルを使用します。 この記事では、モデルをカスタマイズする方法について説明し Identity ます。

## <a name="identity-and-ef-core-migrations"></a>Identityおよび EF Core 移行

モデルを調べる前に、 Identity [EF Core の移行](/ef/core/managing-schemas/migrations/)を使用してデータベースを作成および更新する方法を理解しておくと役に立ちます。 最上位レベルでは、プロセスは次のようになります。

1. [コードでデータモデル](/ef/core/modeling/)を定義または更新します。
1. このモデルをデータベースに適用できる変更に変換するには、移行を追加します。
1. 移行によって意図が正しく表現されていることを確認します。
1. モデルと同期するようにデータベースを更新するには、移行を適用します。
1. 手順 1. ~ 4. を繰り返して、モデルをさらに調整し、データベースを同期させます。

移行を追加して適用するには、次のいずれかの方法を使用します。

* Visual Studio を使用している場合は、[**パッケージマネージャーコンソール**(PMC)] ウィンドウ。 詳細については、「 [EF CORE PMC ツール](/ef/core/miscellaneous/cli/powershell)」を参照してください。
* コマンドラインを使用している場合は、.NET Core CLI ます。 詳細については、「 [EF Core .net コマンドラインツール](/ef/core/miscellaneous/cli/dotnet)」を参照してください。
* アプリの実行時に、エラーページの [**移行の適用**] ボタンをクリックします。

ASP.NET Core には、開発時エラーページハンドラーがあります。 ハンドラーは、アプリの実行時に移行を適用できます。 実稼働アプリでは、通常、移行から SQL スクリプトを生成し、制御されたアプリとデータベースの配置の一部としてデータベースの変更をデプロイします。

を使用する新しいアプリを作成すると Identity 、上記の手順 1. と 2. は既に完了しています。 つまり、初期データモデルが既に存在し、初期移行がプロジェクトに追加されています。 初期移行は、引き続きデータベースに適用する必要があります。 最初の移行は、次のいずれかの方法を使用して適用できます。

* `Update-Database`PMC でを実行します。
* `dotnet ef database update`コマンドシェルでを実行します。
* アプリの実行時に、エラーページの [**移行の適用**] ボタンをクリックします。

モデルが変更されたときに、上記の手順を繰り返します。

## <a name="the-identity-model"></a>Identityモデル

### <a name="entity-types"></a>エンティティの種類

モデルは、 Identity 次のエンティティ型で構成されます。

|エンティティの種類|[説明]                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |ユーザーを表します。                                         |
|`Role`     |ロールを表します。                                           |
|`UserClaim`|ユーザーが所有するクレームを表します。                    |
|`UserToken`|ユーザーの認証トークンを表します。               |
|`UserLogin`|ユーザーをログインに関連付けます。                              |
|`RoleClaim`|ロール内のすべてのユーザーに付与されるクレームを表します。|
|`UserRole` |ユーザーとロールを関連付ける結合エンティティ。               |

### <a name="entity-type-relationships"></a>エンティティ型のリレーションシップ

[エンティティ型](#entity-types)は、次の方法で相互に関連付けられます。

* 各には `User` 多くのを含めることができ `UserClaims` ます。
* 各には `User` 多くのを含めることができ `UserLogins` ます。
* 各には `User` 多くのを含めることができ `UserTokens` ます。
* それぞれに `Role` 関連付けることができ `RoleClaims` ます。
* それぞれに `User` 関連付けることができ `Roles` 、各を `Role` 多くのに関連付けることができ `Users` ます。 これは、データベース内の結合テーブルを必要とする多対多リレーションシップです。 結合テーブルは、エンティティによって表され `UserRole` ます。

### <a name="default-model-configuration"></a>既定のモデル構成

Identityモデルを構成して使用するために[Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)から継承する多くの*コンテキストクラス*を定義します。 この構成は、コンテキストクラスの[Onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)メソッドで[EF CORE Code First Fluent API](/ef/core/modeling/)を使用して行います。 既定の構成は次のとおりです。

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>モデルのジェネリック型

Identity上に示した各エンティティ型に対して、既定の[共通言語ランタイム](/dotnet/standard/glossary#clr)(CLR) 型を定義します。 これらの型のすべてにプレフィックスが付いてい *Identity* ます。

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

これらの型を直接使用するのではなく、アプリケーション独自の型の基底クラスとして型を使用できます。 `DbContext`によって定義さ Identity れるクラスはジェネリックであるため、モデル内の1つ以上のエンティティ型に異なる CLR 型を使用できます。 これらのジェネリック型を使用すると、 `User` 主キー (PK) データ型を変更することもできます。

ロールのサポートを使用する場合は Identity 、 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> クラスを使用する必要があります。 次に例を示します。

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Identityロールなし (要求のみ) を使用することもできます。この場合、 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> クラスを使用する必要があります。

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>モデルをカスタマイズする

モデルのカスタマイズの開始点は、適切なコンテキスト型から派生することです。 「[モデルのジェネリック型](#model-generic-types)」を参照してください。 このコンテキストの種類は通常と呼ばれ、 `ApplicationDbContext` ASP.NET Core テンプレートによって作成されます。

このコンテキストは、次の2つの方法でモデルを構成するために使用されます。

* ジェネリック型パラメーターのエンティティ型とキー型を提供します。
* をオーバーライドし `OnModelCreating` て、これらの型のマッピングを変更します。

をオーバーライドする場合は `OnModelCreating` 、最初にを `base.OnModelCreating` 呼び出す必要があります。次に、オーバーライドする構成を呼び出す必要があります。 EF Core は、一般に、構成のための最後の1つの wins ポリシーを持っています。 たとえば、 `ToTable` エンティティ型のメソッドが最初に1つのテーブル名で呼び出され、後で別のテーブル名を使用して呼び出された場合、2番目の呼び出しのテーブル名が使用されます。

### <a name="custom-user-data"></a>カスタムユーザーデータ

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[カスタムユーザーデータ](xref:security/authentication/add-user-data)は、から継承することによってサポートされ `IdentityUser` ます。 この型には、次のような名前が付いてい `ApplicationUser` ます。

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

この型は、 `ApplicationUser` コンテキストの汎用引数として使用します。

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

クラスでオーバーライドする必要はありません `OnModelCreating` `ApplicationDbContext` 。 EF Core は、 `CustomTag` 規則に従ってプロパティをマップします。 ただし、新しい列を作成するには、データベースを更新する必要があり `CustomTag` ます。 列を作成するには、移行を追加し、「」 [ Identity および「EF Core の移行](#identity-and-ef-core-migrations)」の説明に従ってデータベースを更新します。

*Pages/Shared/_LoginPartial*を更新し、を `IdentityUser` に置き換え `ApplicationUser` ます。

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

*区分/ Identity /IdentityHostingStartup.cs*を更新するか `Startup.ConfigureServices` 、をに置き換え `IdentityUser` `ApplicationUser` ます。

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

ASP.NET Core 2.1 以降では、 Identity がクラスライブラリとして提供され Razor ます。 詳細については、「<xref:security/authentication/scaffold-identity>」を参照してください。 そのため、上記のコードでは、を呼び出す必要があり <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> ます。 Scaffolder を Identity 使用してファイルをプロジェクトに追加した場合は、への呼び出しを削除し Identity `AddDefaultUI` ます。 詳細については次を参照してください:

* [スキャフォールディングIdentity](xref:security/authentication/scaffold-identity)
* [カスタムユーザーデータを追加、ダウンロード、および削除するIdentity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>主キーの種類を変更する

データベースを作成した後に PK 列のデータ型を変更すると、多くのデータベースシステムで問題が発生します。 PK を変更するには、通常、テーブルを削除してから再作成する必要があります。 そのため、データベースの作成時に、初期移行でキーの種類を指定する必要があります。

PK の種類を変更するには、次の手順に従います。

1. PK が変更される前にデータベースが作成された場合は、 `Drop-Database` (PMC) または `dotnet ef database drop` (.NET Core CLI) を実行して削除します。
2. データベースの削除を確認した後、 `Remove-Migration` (PMC) または (.NET Core CLI) を使用して最初の移行を削除し `dotnet ef migrations remove` ます。
3. `ApplicationDbContext`から派生するようにクラスを更新 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> します。 の新しいキーの種類を指定し `TKey` ます。 たとえば、キーの種類を使用するには、次のように `Guid` 入力します。

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> とは、 <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> 新しいキー型を使用するように指定する必要があります。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> とは、 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> 新しいキー型を使用するように指定する必要があります。

    ::: moniker-end

    `Startup.ConfigureServices`汎用ユーザーを使用するには、次のように更新する必要があります。

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. カスタムクラスが使用されている場合は `ApplicationUser` 、継承元のクラスを更新し `IdentityUser` ます。 次に例を示します。

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    `ApplicationDbContext`カスタムクラスを参照するように更新し `ApplicationUser` ます。

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    でサービスを追加するときに、カスタムデータベースコンテキストクラスを登録し Identity `Startup.ConfigureServices` ます。

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ASP.NET Core 2.1 以降では、 Identity がクラスライブラリとして提供され Razor ます。 詳細については、「<xref:security/authentication/scaffold-identity>」を参照してください。 そのため、上記のコードでは、を呼び出す必要があり <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> ます。 Scaffolder を Identity 使用してファイルをプロジェクトに追加した場合は、への呼び出しを削除し Identity `AddDefaultUI` ます。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> `TKey` 主キーのデータ型を示す型を受け取ります。

    ::: moniker-end

5. カスタムクラスが使用されている場合は `ApplicationRole` 、継承元のクラスを更新し `IdentityRole<TKey>` ます。 次に例を示します。

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    `ApplicationDbContext`カスタムクラスを参照するように更新し `ApplicationRole` ます。 たとえば、次のクラスはカスタム `ApplicationUser` とカスタムを参照し `ApplicationRole` ます。

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    でサービスを追加するときに、カスタムデータベースコンテキストクラスを登録し Identity `Startup.ConfigureServices` ます。

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ASP.NET Core 2.1 以降では、 Identity がクラスライブラリとして提供され Razor ます。 詳細については、「<xref:security/authentication/scaffold-identity>」を参照してください。 そのため、上記のコードでは、を呼び出す必要があり <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> ます。 Scaffolder を Identity 使用してファイルをプロジェクトに追加した場合は、への呼び出しを削除し Identity `AddDefaultUI` ます。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    でサービスを追加するときに、カスタムデータベースコンテキストクラスを登録し Identity `Startup.ConfigureServices` ます。

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    でサービスを追加するときに、カスタムデータベースコンテキストクラスを登録し Identity `Startup.ConfigureServices` ます。

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    メソッドは、 <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> `TKey` 主キーのデータ型を示す型を受け取ります。

    ::: moniker-end

### <a name="add-navigation-properties"></a>ナビゲーションプロパティの追加

リレーションシップのモデル構成の変更は、他の変更を行うよりも困難になることがあります。 新しい追加のリレーションシップを作成するのではなく、既存のリレーションシップを置き換える必要があります。 特に、変更されたリレーションシップでは、既存のリレーションシップと同じ外部キー (FK) プロパティを指定する必要があります。 たとえば、との間の `Users` リレーションシップ `UserClaims` は、既定では次のように指定されています。

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

このリレーションシップの FK は、プロパティとして指定され `UserClaim.UserId` ます。 `HasMany`および `WithOne` は、ナビゲーションプロパティを使用せずにリレーションシップを作成するための引数なしで呼び出されます。

`ApplicationUser`関連するをユーザーから参照できるようにするナビゲーションプロパティをに追加し `UserClaims` ます。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

のは、 `TKey` `IdentityUserClaim<TKey>` ユーザーの PK に対して指定された型です。 この場合、 `TKey` は `string` 既定値が使用されているためです。 エンティティ型の PK 型では**ありません** `UserClaim` 。

ナビゲーションプロパティが存在するようになったので、次のように構成する必要があり `OnModelCreating` ます。

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

リレーションシップは前とまったく同じように構成されていることに注意してください。の呼び出しで指定されたナビゲーションプロパティを使用し `HasMany` ます。

ナビゲーションプロパティは、データベースではなく EF モデルにのみ存在します。 リレーションシップの FK は変更されていないため、この種のモデルの変更では、データベースを更新する必要はありません。 これは、変更を行った後に移行を追加することによって確認できます。 `Up`メソッドと `Down` メソッドは空です。

### <a name="add-all-user-navigation-properties"></a>すべてのユーザーナビゲーションプロパティを追加する

次の例では、上のセクションをガイダンスとして使用して、ユーザーのすべてのリレーションシップに対して一方向のナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>ユーザーおよびロールのナビゲーションプロパティの追加

次の例では、上のセクションをガイダンスとして使用して、ユーザーとロールのすべてのリレーションシップに対してナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

メモ:

* この例には、 `UserRole` ユーザーからロールへの多対多リレーションシップを移動するために必要な join エンティティも含まれています。
* 型ではなく型が使用されていることを反映するように、ナビゲーションプロパティの型を変更することを忘れないで `ApplicationXxx` `IdentityXxx` ください。
* ジェネリック定義でを使用することを忘れない `ApplicationXxx` で `ApplicationContext` ください。

### <a name="add-all-navigation-properties"></a>すべてのナビゲーションプロパティの追加

次の例では、上のセクションをガイダンスとして使用して、すべてのエンティティ型のすべてのリレーションシップに対してナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>複合キーを使用する

前のセクションでは、モデルで使用されるキーの種類の変更について説明し Identity ます。 Identity複合キーを使用するようにキーモデルを変更することはサポートされていないか、推奨されません。 で複合キーを使用する場合は、 Identity マネージャーコードがモデルとどのように連携するかを変更する必要が Identity あります。 このカスタマイズについては、このドキュメントでは説明しません。

### <a name="change-tablecolumn-names-and-facets"></a>テーブル/列の名前とファセットの変更

テーブルと列の名前を変更するには、を呼び出し `base.OnModelCreating` ます。 次に、構成を追加して、既定値を上書きします。 たとえば、すべてのテーブルの名前を変更するには、次のようにし Identity ます。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

これらの例では、既定の型を使用し Identity ます。 などのアプリの種類を使用する場合は `ApplicationUser` 、既定の型ではなく、その型を構成します。

次の例では、いくつかの列名を変更します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

一部の種類のデータベース列は、特定の*ファセット*を使用して構成でき `string` ます (許容される最大長など)。 次の例では、モデルのいくつかのプロパティの列の最大長を設定し `string` ます。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>別のスキーマにマップする

スキーマは、データベースプロバイダーによって動作が異なります。 SQL Server の場合、既定では*dbo*スキーマのすべてのテーブルが作成されます。 テーブルは、別のスキーマで作成できます。 次に例を示します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>遅延読み込み

このセクションでは、モデルでの遅延読み込みプロキシのサポートを Identity 追加します。 遅延読み込みは、ナビゲーションプロパティが読み込まれていることを確認せずに使用できるため便利です。

エンティティ型は、 [EF Core のドキュメント](/ef/core/querying/related-data#lazy-loading)で説明されているように、いくつかの方法で遅延読み込みに適したものにすることができます。 わかりやすくするために、レイジー読み込みプロキシを使用します。これには次のものが必要です。

* [Microsoft EntityFrameworkCore プロキシ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージのインストール。
* <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)内のへの呼び出し。
* ナビゲーションプロパティを持つパブリックエンティティ型 `public virtual` 。

でを呼び出す例を次に示し `UseLazyLoadingProxies` `Startup.ConfigureServices` ます。

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

エンティティ型へのナビゲーションプロパティの追加に関するガイダンスについては、前の例を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authentication/scaffold-identity>

::: moniker-end
