---
title: ASP.NET Core 用のカスタムストレージプロバイダーIdentity
author: ardalis
description: ASP.NET Core 用にカスタム記憶域プロバイダーを構成する方法について説明 Identity します。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 137b73529a6c3d2a1dece201ebd8a7a5a96da349
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404744"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="48382-103">ASP.NET Core 用のカスタムストレージプロバイダーIdentity</span><span class="sxs-lookup"><span data-stu-id="48382-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="48382-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="48382-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="48382-105">ASP.NET Core Identity は拡張可能なシステムであり、カスタム記憶域プロバイダーを作成してアプリに接続することができます。</span><span class="sxs-lookup"><span data-stu-id="48382-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="48382-106">このトピックでは、ASP.NET Core 用にカスタマイズされた記憶域プロバイダーを作成する方法について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="48382-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="48382-107">この記事では、独自の記憶域プロバイダーを作成するための重要な概念について説明しますが、詳細な手順については説明しません。</span><span class="sxs-lookup"><span data-stu-id="48382-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="48382-108">[GitHub のサンプルを表示またはダウンロードしてください](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample)。</span><span class="sxs-lookup"><span data-stu-id="48382-108">[View or download sample from GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample).</span></span>

## <a name="introduction"></a><span data-ttu-id="48382-109">はじめに</span><span class="sxs-lookup"><span data-stu-id="48382-109">Introduction</span></span>

<span data-ttu-id="48382-110">既定では、ASP.NET Core Identity システムは Entity Framework Core を使用して SQL Server データベースにユーザー情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="48382-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="48382-111">多くのアプリでは、この方法が適しています。</span><span class="sxs-lookup"><span data-stu-id="48382-111">For many apps, this approach works well.</span></span> <span data-ttu-id="48382-112">ただし、別の永続化メカニズムまたはデータスキーマを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="48382-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="48382-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48382-113">For example:</span></span>

* <span data-ttu-id="48382-114">[Azure Table Storage](/azure/storage/)または別のデータストアを使用します。</span><span class="sxs-lookup"><span data-stu-id="48382-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="48382-115">データベーステーブルの構造が異なります。</span><span class="sxs-lookup"><span data-stu-id="48382-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="48382-116">[Dapper](https://github.com/StackExchange/Dapper)など、別のデータアクセス方法を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="48382-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="48382-117">これらの各ケースでは、ストレージメカニズム用にカスタマイズされたプロバイダーを作成し、そのプロバイダーをアプリに接続できます。</span><span class="sxs-lookup"><span data-stu-id="48382-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="48382-118">ASP.NET Core Identity は、Visual Studio の [個々のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="48382-119">.NET Core CLI を使用する場合は、次のように追加し `-au Individual` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="48382-120">ASP.NET Core Identity アーキテクチャ</span><span class="sxs-lookup"><span data-stu-id="48382-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="48382-121">ASP.NET Core Identity は、マネージャーとストアと呼ばれるクラスで構成されます。</span><span class="sxs-lookup"><span data-stu-id="48382-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="48382-122">*マネージャー*は、アプリ開発者がユーザーの作成などの操作を実行するために使用する高レベルのクラスです Identity 。</span><span class="sxs-lookup"><span data-stu-id="48382-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="48382-123">*ストア*は、ユーザーやロールなどのエンティティがどのように永続化されるかを指定する下位レベルのクラスです。</span><span class="sxs-lookup"><span data-stu-id="48382-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="48382-124">ストアはリポジトリパターンに従い、永続化メカニズムと密接に結び付いています。</span><span class="sxs-lookup"><span data-stu-id="48382-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="48382-125">マネージャーはストアから切り離されています。つまり、アプリケーションコードを変更することなく永続化メカニズムを置き換えることができます (構成を除く)。</span><span class="sxs-lookup"><span data-stu-id="48382-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="48382-126">次の図は、web アプリがマネージャーと対話する方法を示しています。また、ストアはデータアクセス層と対話します。</span><span class="sxs-lookup"><span data-stu-id="48382-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core アプリは、マネージャー (たとえば、' UserManager ', ' RoleManager ') と連携します。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="48382-129">カスタム記憶域プロバイダーを作成するには、データソース、データアクセス層、およびこのデータアクセス層と対話するストアクラスを作成します (上の図の緑と灰色のボックス)。</span><span class="sxs-lookup"><span data-stu-id="48382-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="48382-130">マネージャーや、それらと対話するアプリコード (上の青いボックス) をカスタマイズする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="48382-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="48382-131">の新しいインスタンスを作成する場合、 `UserManager` または `RoleManager` ユーザークラスの型を指定し、ストアクラスのインスタンスを引数として渡します。</span><span class="sxs-lookup"><span data-stu-id="48382-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="48382-132">この方法を使用すると、カスタマイズしたクラスを ASP.NET Core に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="48382-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="48382-133">[新しいストレージプロバイダーを使用するようにアプリを再構成](#reconfigure-app-to-use-a-new-storage-provider)する `UserManager` カスタムストアでとをインスタンス化する方法について説明し `RoleManager` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="48382-134">Identityデータ型を格納 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48382-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="48382-135">[ASP.NET Core Identity ](https://github.com/aspnet/identity)データ型の詳細については、次のセクションを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="48382-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="48382-136">ユーザー</span><span class="sxs-lookup"><span data-stu-id="48382-136">Users</span></span>

<span data-ttu-id="48382-137">Web サイトの登録済みユーザー。</span><span class="sxs-lookup"><span data-stu-id="48382-137">Registered users of your web site.</span></span> <span data-ttu-id="48382-138">[ユーザー](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)の種類は、独自のカスタム型の例として拡張または使用できます。</span><span class="sxs-lookup"><span data-stu-id="48382-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="48382-139">独自のカスタム id ストレージソリューションを実装するために、特定の型から継承する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="48382-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="48382-140">ユーザー要求</span><span class="sxs-lookup"><span data-stu-id="48382-140">User Claims</span></span>

<span data-ttu-id="48382-141">ユーザーの id を表す、ユーザーに関する一連のステートメント (または[要求](/dotnet/api/system.security.claims.claim))。</span><span class="sxs-lookup"><span data-stu-id="48382-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="48382-142">では、ロールを使用した場合よりも多くのユーザー id を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="48382-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="48382-143">ユーザーログイン</span><span class="sxs-lookup"><span data-stu-id="48382-143">User Logins</span></span>

<span data-ttu-id="48382-144">ユーザーのログイン時に使用する外部認証プロバイダー (Facebook や Microsoft アカウントなど) に関する情報。</span><span class="sxs-lookup"><span data-stu-id="48382-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="48382-145">例</span><span class="sxs-lookup"><span data-stu-id="48382-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="48382-146">役割</span><span class="sxs-lookup"><span data-stu-id="48382-146">Roles</span></span>

<span data-ttu-id="48382-147">サイトの承認グループ。</span><span class="sxs-lookup"><span data-stu-id="48382-147">Authorization groups for your site.</span></span> <span data-ttu-id="48382-148">ロール Id とロール名 ("Admin" や "Employee" など) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="48382-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="48382-149">例</span><span class="sxs-lookup"><span data-stu-id="48382-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="48382-150">データアクセス層</span><span class="sxs-lookup"><span data-stu-id="48382-150">The data access layer</span></span>

<span data-ttu-id="48382-151">このトピックでは、使用する永続化メカニズムについて理解していること、およびそのメカニズムのエンティティを作成する方法について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="48382-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="48382-152">このトピックでは、リポジトリまたはデータアクセスクラスの作成方法の詳細については説明しません。ASP.NET Core を使用する場合の設計上の決定について、いくつかの提案を提供 Identity します。</span><span class="sxs-lookup"><span data-stu-id="48382-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="48382-153">カスタマイズされたストアプロバイダーのデータアクセス層を設計する際には、自由度が高くなります。</span><span class="sxs-lookup"><span data-stu-id="48382-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="48382-154">アプリで使用する予定の機能に対してのみ、永続化メカニズムを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="48382-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="48382-155">たとえば、アプリでロールを使用していない場合は、ロールまたはユーザーロールの関連付け用のストレージを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="48382-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="48382-156">テクノロジと既存のインフラストラクチャでは、ASP.NET Core の既定の実装とは大きく異なる構造が必要になる場合があり Identity ます。</span><span class="sxs-lookup"><span data-stu-id="48382-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="48382-157">データアクセス層では、ストレージ実装の構造を操作するロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="48382-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="48382-158">データアクセス層は、ASP.NET Core からデータソースにデータを保存するためのロジックを提供し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="48382-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="48382-159">カスタマイズした記憶域プロバイダーのデータアクセス層には、ユーザーとロールの情報を格納するための次のクラスが含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="48382-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="48382-160">Context クラス</span><span class="sxs-lookup"><span data-stu-id="48382-160">Context class</span></span>

<span data-ttu-id="48382-161">永続化メカニズムに接続してクエリを実行するための情報をカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="48382-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="48382-162">いくつかのデータクラスには、通常、依存関係の挿入によって提供されるこのクラスのインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="48382-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="48382-163">[例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="48382-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="48382-164">ユーザーストレージ</span><span class="sxs-lookup"><span data-stu-id="48382-164">User Storage</span></span>

<span data-ttu-id="48382-165">ユーザー情報 (ユーザー名やパスワードハッシュなど) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="48382-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="48382-166">例</span><span class="sxs-lookup"><span data-stu-id="48382-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="48382-167">ロールストレージ</span><span class="sxs-lookup"><span data-stu-id="48382-167">Role Storage</span></span>

<span data-ttu-id="48382-168">ロール名などのロール情報を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="48382-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="48382-169">例</span><span class="sxs-lookup"><span data-stu-id="48382-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="48382-170">UserClaims ストレージ</span><span class="sxs-lookup"><span data-stu-id="48382-170">UserClaims Storage</span></span>

<span data-ttu-id="48382-171">ユーザー要求情報 (要求の種類や値など) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="48382-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="48382-172">例</span><span class="sxs-lookup"><span data-stu-id="48382-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="48382-173">UserLogins ストレージ</span><span class="sxs-lookup"><span data-stu-id="48382-173">UserLogins Storage</span></span>

<span data-ttu-id="48382-174">ユーザーのログイン情報 (外部認証プロバイダーなど) を格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="48382-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="48382-175">例</span><span class="sxs-lookup"><span data-stu-id="48382-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="48382-176">UserRole ストレージ</span><span class="sxs-lookup"><span data-stu-id="48382-176">UserRole Storage</span></span>

<span data-ttu-id="48382-177">どのロールがどのユーザーに割り当てられているかを格納および取得します。</span><span class="sxs-lookup"><span data-stu-id="48382-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="48382-178">例</span><span class="sxs-lookup"><span data-stu-id="48382-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="48382-179">**ヒント:** アプリで使用する予定のクラスのみを実装します。</span><span class="sxs-lookup"><span data-stu-id="48382-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="48382-180">データアクセスクラスで、永続化メカニズムのデータ操作を実行するコードを指定します。</span><span class="sxs-lookup"><span data-stu-id="48382-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="48382-181">たとえば、カスタムプロバイダー内で、*ストア*クラスに新しいユーザーを作成するには、次のコードが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="48382-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="48382-182">ユーザーを作成するための実装ロジックは、次に示すメソッドに含まれてい `_usersTable.CreateAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="48382-183">ユーザークラスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="48382-183">Customize the user class</span></span>

<span data-ttu-id="48382-184">ストレージプロバイダーを実装する場合は、ユーザークラスを作成します。このクラスは、[ユーザークラスに](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)相当します。</span><span class="sxs-lookup"><span data-stu-id="48382-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="48382-185">少なくとも、ユーザークラスにはプロパティとプロパティが含まれている必要があり `Id` `UserName` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="48382-186">`IdentityUser`クラスは、要求された操作の実行時にが呼び出すプロパティを定義し `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="48382-187">プロパティの既定の型 `Id` は文字列ですが、から継承して別の型を指定することもでき `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="48382-188">フレームワークでは、ストレージの実装がデータ型の変換を処理することを想定しています。</span><span class="sxs-lookup"><span data-stu-id="48382-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="48382-189">ユーザーストアをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="48382-189">Customize the user store</span></span>

<span data-ttu-id="48382-190">ユーザーの `UserStore` すべてのデータ操作のメソッドを提供するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="48382-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="48382-191">このクラスは、 [Userstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)クラスに相当します。</span><span class="sxs-lookup"><span data-stu-id="48382-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="48382-192">クラスで `UserStore` 、 `IUserStore<TUser>` および必要に応じて省略可能なインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="48382-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="48382-193">実装するオプションのインターフェイスは、アプリで提供される機能に基づいて選択します。</span><span class="sxs-lookup"><span data-stu-id="48382-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="48382-194">省略可能なインターフェイス</span><span class="sxs-lookup"><span data-stu-id="48382-194">Optional interfaces</span></span>

* [<span data-ttu-id="48382-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="48382-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="48382-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="48382-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="48382-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="48382-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="48382-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="48382-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="48382-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="48382-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="48382-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="48382-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="48382-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="48382-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="48382-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="48382-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="48382-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="48382-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="48382-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="48382-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="48382-205">オプションのインターフェイスは、から継承され `IUserStore<TUser>` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="48382-206">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)には、部分的に実装されたサンプルユーザーストアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="48382-206">You can see a partially implemented sample user store in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="48382-207">クラス内では、 `UserStore` 操作を実行するために作成したデータアクセスクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="48382-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="48382-208">これらは、依存関係の挿入を使用して渡されます。</span><span class="sxs-lookup"><span data-stu-id="48382-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="48382-209">たとえば、Dapper 実装の SQL Server では、クラスに `UserStore` は、 `CreateAsync` のインスタンスを使用して `DapperUsersTable` 新しいレコードを挿入するメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="48382-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="48382-210">ユーザーストアをカスタマイズするときに実装するインターフェイス</span><span class="sxs-lookup"><span data-stu-id="48382-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="48382-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="48382-211">**IUserStore**</span></span>  
 <span data-ttu-id="48382-212">[IUserStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)インターフェイスは、ユーザーストアに実装する必要がある唯一のインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="48382-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="48382-213">ユーザーの作成、更新、削除、および取得を行うためのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="48382-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="48382-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="48382-215">[IUserClaimStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)インターフェイスでは、ユーザーの信頼性情報を有効にするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="48382-216">これには、ユーザー要求を追加、削除、および取得するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="48382-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="48382-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="48382-218">[IUserLoginStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)は、外部認証プロバイダーを有効にするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="48382-219">これには、ユーザーログインを追加、削除、取得するためのメソッド、およびログイン情報に基づいてユーザーを取得するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="48382-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="48382-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="48382-221">[IUserRoleStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)インターフェイスでは、ユーザーをロールにマップするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="48382-222">これには、ユーザーのロールを追加、削除、取得するメソッド、およびユーザーがロールに割り当てられているかどうかを確認するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="48382-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="48382-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="48382-224">[IUserPasswordStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)インターフェイスは、ハッシュされたパスワードを保持するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="48382-225">ハッシュされたパスワードを取得および設定するためのメソッドと、ユーザーがパスワードを設定したかどうかを示すメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="48382-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="48382-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="48382-227">[IUserSecurityStampStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)インターフェイスでは、ユーザーのアカウント情報が変更されたかどうかを示すセキュリティスタンプを使用するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="48382-228">このスタンプは、ユーザーがパスワードを変更したとき、またはログインを追加または削除したときに更新されます。</span><span class="sxs-lookup"><span data-stu-id="48382-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="48382-229">セキュリティスタンプを取得および設定するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="48382-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="48382-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="48382-231">[IUserTwoFactorStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)インターフェイスでは、2要素認証をサポートするために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="48382-232">これには、ユーザーに対して2要素認証を有効にするかどうかを取得および設定するためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="48382-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="48382-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="48382-234">[IUserPhoneNumberStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)インターフェイスでは、ユーザーの電話番号を格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="48382-235">電話番号を取得して設定するためのメソッド、および電話番号を確認する方法が含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="48382-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="48382-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="48382-237">[IUserEmailStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)インターフェイスでは、ユーザーの電子メールアドレスを格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="48382-238">このファイルには、電子メールアドレスを取得および設定するためのメソッドと、電子メールを確認するかどうかが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="48382-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="48382-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="48382-240">[IUserLockoutStore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)インターフェイスは、アカウントのロックに関する情報を格納するために実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="48382-241">失敗したアクセス試行とロックアウトを追跡するメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="48382-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="48382-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="48382-243">[Iqueryableuserstore &lt; tuser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)インターフェイスは、クエリ可能なユーザーストアを提供するために実装するメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="48382-244">アプリケーションで必要なインターフェイスだけを実装します。</span><span class="sxs-lookup"><span data-stu-id="48382-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="48382-245">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48382-245">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="48382-246">ユーザー Id、ユーザー名、およびユーザー名</span><span class="sxs-lookup"><span data-stu-id="48382-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="48382-247">名前空間には、ユーザー `Microsoft.AspNet.Identity.EntityFramework` [id](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)クラスの実装が含まれてい[IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)ます。 [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)</span><span class="sxs-lookup"><span data-stu-id="48382-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="48382-248">これらの機能を使用している場合は、これらのクラスの独自のバージョンを作成し、アプリのプロパティを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="48382-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="48382-249">ただし、基本操作 (ユーザーの要求の追加や削除など) を実行するときに、これらのエンティティをメモリに読み込まない方が効率的な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="48382-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="48382-250">代わりに、バックエンドストアクラスは、データソースでこれらの操作を直接実行できます。</span><span class="sxs-lookup"><span data-stu-id="48382-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="48382-251">たとえば、メソッドは、メソッドを呼び出して、 `UserStore.GetClaimsAsync` `userClaimTable.FindByUserId(user.Id)` そのテーブルに対してクエリを直接実行し、クレームの一覧を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="48382-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="48382-252">ロールクラスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="48382-252">Customize the role class</span></span>

<span data-ttu-id="48382-253">ロールストレージプロバイダーを実装する場合は、カスタムロールの種類を作成できます。</span><span class="sxs-lookup"><span data-stu-id="48382-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="48382-254">特定のインターフェイスを実装する必要はありませんが、が必要であり、 `Id` 通常はプロパティを持つ必要があり `Name` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="48382-255">ロールクラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="48382-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="48382-256">ロールストアをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="48382-256">Customize the role store</span></span>

<span data-ttu-id="48382-257">`RoleStore`ロールに対するすべてのデータ操作のメソッドを提供するクラスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="48382-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="48382-258">このクラスは、 [Rolestore &lt; trole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)クラスに相当します。</span><span class="sxs-lookup"><span data-stu-id="48382-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="48382-259">クラスで `RoleStore` は、および必要に応じてインターフェイスを実装し `IRoleStore<TRole>` `IQueryableRoleStore<TRole>` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="48382-260">**IRoleStore &lt; trole&gt;**</span><span class="sxs-lookup"><span data-stu-id="48382-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="48382-261">[Irolestore &lt; trole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)インターフェイスは、ロールストアクラスに実装するメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="48382-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="48382-262">これには、ロールの作成、更新、削除、および取得を行うためのメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="48382-263">**RoleStore &lt; trole&gt;**</span><span class="sxs-lookup"><span data-stu-id="48382-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="48382-264">カスタマイズするには `RoleStore` 、インターフェイスを実装するクラスを作成し `IRoleStore<TRole>` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="48382-265">新しい記憶域プロバイダーを使用するようにアプリを再構成する</span><span class="sxs-lookup"><span data-stu-id="48382-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="48382-266">ストレージプロバイダーを実装したら、それを使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="48382-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="48382-267">アプリで既定のプロバイダーが使用されている場合は、カスタムプロバイダーに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="48382-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="48382-268">`Microsoft.AspNetCore.EntityFramework.Identity`NuGet パッケージを削除します。</span><span class="sxs-lookup"><span data-stu-id="48382-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="48382-269">ストレージプロバイダーが別のプロジェクトまたはパッケージに存在する場合は、その記憶域プロバイダーへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="48382-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="48382-270">へのすべての参照を、 `Microsoft.AspNetCore.EntityFramework.Identity` ストレージプロバイダーの名前空間の using ステートメントに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="48382-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="48382-271">メソッドで、 `ConfigureServices` `AddIdentity` カスタム型を使用するようにメソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="48382-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="48382-272">この目的のために独自の拡張メソッドを作成できます。</span><span class="sxs-lookup"><span data-stu-id="48382-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="48382-273">例については、「 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="48382-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="48382-274">ロールを使用している場合は、 `RoleManager` クラスを使用するようにを更新し `RoleStore` ます。</span><span class="sxs-lookup"><span data-stu-id="48382-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="48382-275">接続文字列と資格情報をアプリの構成に更新します。</span><span class="sxs-lookup"><span data-stu-id="48382-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="48382-276">例:</span><span class="sxs-lookup"><span data-stu-id="48382-276">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="48382-277">References</span><span class="sxs-lookup"><span data-stu-id="48382-277">References</span></span>

* <span data-ttu-id="48382-278">[ASP.NET 4.x 用のカスタムストレージプロバイダーIdentity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span><span class="sxs-lookup"><span data-stu-id="48382-278">[Custom Storage Providers for ASP.NET 4.x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span></span>
* <span data-ttu-id="48382-279">[ASP.NET Core Identity ](https://github.com/dotnet/AspNetCore/tree/master/src/Identity): このリポジトリには、コミュニティによって管理されるストアプロバイダーへのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="48382-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity): This repository includes links to community maintained store providers.</span></span>
