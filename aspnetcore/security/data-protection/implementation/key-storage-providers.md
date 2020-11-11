---
title: ASP.NET Core のキー記憶域プロバイダー
author: rick-anderson
description: ASP.NET Core の主要な記憶域プロバイダーと、キーの格納場所を構成する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 6a70183ce4b1a129ef213300473b233a5ef822f9
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94463887"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="90ad2-103">ASP.NET Core のキー記憶域プロバイダー</span><span class="sxs-lookup"><span data-stu-id="90ad2-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="90ad2-104">データ保護システムでは、暗号化キーの保存先を決定するために、 [既定で検出メカニズム](xref:security/data-protection/configuration/default-settings) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="90ad2-105">開発者は、既定の検出メカニズムを上書きし、場所を手動で指定できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="90ad2-106">明示的なキーの保存場所を指定した場合、データ保護システムは解除の既定のキー暗号化メカニズムを使用するので、保存時にキーが暗号化されなくなります。</span><span class="sxs-lookup"><span data-stu-id="90ad2-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="90ad2-107">運用環境のデプロイで [は、明示的なキー暗号化メカニズム](xref:security/data-protection/implementation/key-encryption-at-rest) を追加で指定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="90ad2-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="90ad2-108">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="90ad2-108">File system</span></span>

<span data-ttu-id="90ad2-109">ファイルシステムベースのキーリポジトリを構成するには、次に示すように、 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 構成ルーチンを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="90ad2-110">キーを格納するリポジトリを指す [DirectoryInfo](/dotnet/api/system.io.directoryinfo) を指定します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="90ad2-111">は、 `DirectoryInfo` ローカルコンピューター上のディレクトリを指すことも、ネットワーク共有上のフォルダーを指すこともできます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="90ad2-112">ローカルコンピューター上のディレクトリを指している場合 (つまり、このリポジトリを使用するためにアクセスが必要なのはローカルコンピューター上のアプリのみです)、windows [DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (windows) を使用して保存時のキーを暗号化することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="90ad2-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="90ad2-113">それ以外の場合は、 [x.509 証明書](xref:security/data-protection/implementation/key-encryption-at-rest) を使用して保存時のキーを暗号化することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="90ad2-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="90ad2-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="90ad2-114">Azure Storage</span></span>

<span data-ttu-id="90ad2-115">[AspNetCore](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)パッケージを使用すると、Azure Blob Storage にデータ保護キーを格納できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-115">The [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="90ad2-116">キーは、web アプリの複数のインスタンス間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="90ad2-117">アプリは、認証 :::no-loc(cookie)::: s または CSRF 保護を複数のサーバーで共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-117">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="90ad2-118">Azure Blob Storage プロバイダーを構成するには、 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) オーバーロードのいずれかを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="90ad2-119">Web アプリが Azure サービスとして実行されている場合は、接続文字列を使用して azure storage に対する認証を行うことができます。 [blob](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient)を使用します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-119">If the web app is running as an Azure service, connection string can be used to authenticate to Azure storage by using [Azure.Storage.Blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span></span>

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> <span data-ttu-id="90ad2-120">ストレージアカウントへの接続文字列は、Azure Portal の [アクセスキー] セクションで確認するか、次の CLI コマンドを実行して確認できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-120">The connection string to your storage account can be found in the Azure Portal under the "Access Keys" section or by running the following CLI command:</span></span> 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a><span data-ttu-id="90ad2-121">Redis</span><span class="sxs-lookup"><span data-stu-id="90ad2-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="90ad2-122">[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)パッケージは、Redis cache にデータ保護キーを格納することを許可します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="90ad2-123">キーは、web アプリの複数のインスタンス間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="90ad2-124">アプリは、認証 :::no-loc(cookie)::: s または CSRF 保護を複数のサーバーで共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-124">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="90ad2-125">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)パッケージを使用すると、redis cache にデータ保護キーを格納できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="90ad2-126">キーは、web アプリの複数のインスタンス間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="90ad2-127">アプリは、認証 :::no-loc(cookie)::: s または CSRF 保護を複数のサーバーで共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-127">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="90ad2-128">Redis でを構成するには、 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) オーバーロードのいずれかを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="90ad2-129">Redis でを構成するには、 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) オーバーロードのいずれかを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="90ad2-130">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="90ad2-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="90ad2-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="90ad2-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="90ad2-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="90ad2-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="90ad2-133">ASP.NET Core DataProtection のサンプル</span><span class="sxs-lookup"><span data-stu-id="90ad2-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="90ad2-134">レジストリ</span><span class="sxs-lookup"><span data-stu-id="90ad2-134">Registry</span></span>

<span data-ttu-id="90ad2-135">**Windows の展開にのみ適用されます。**</span><span class="sxs-lookup"><span data-stu-id="90ad2-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="90ad2-136">場合によっては、アプリケーションにファイルシステムへの書き込みアクセス権がないことがあります。</span><span class="sxs-lookup"><span data-stu-id="90ad2-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="90ad2-137">アプリが仮想サービスアカウント ( *w3wp.exe* のアプリプール id など) として実行されているシナリオについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="90ad2-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe* 's app pool identity).</span></span> <span data-ttu-id="90ad2-138">このような場合、管理者は、サービスアカウント id によってアクセス可能なレジストリキーをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="90ad2-139">次に示すように、 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="90ad2-140">暗号化キーを格納する場所を指す [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) を指定します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="90ad2-141">Rest でのキーの暗号化には [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="90ad2-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="90ad2-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="90ad2-142">Entity Framework Core</span></span>

<span data-ttu-id="90ad2-143">[AspNetCore コア](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)パッケージは、Entity Framework Core を使用してデータベースにデータ保護キーを格納するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="90ad2-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet パッケージは、プロジェクトファイルに追加する必要があります。これは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)の一部ではありません。</span><span class="sxs-lookup"><span data-stu-id="90ad2-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="90ad2-145">このパッケージでは、web アプリの複数のインスタンス間でキーを共有できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="90ad2-146">EF Core プロバイダーを構成するには、 [Persistkeystodbcontext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="90ad2-147">ジェネリックパラメーターは、 `TContext` [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から継承し、 [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="90ad2-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="90ad2-148">`DataProtectionKeys` テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90ad2-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ad2-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="90ad2-150">**パッケージマネージャーコンソール** (PMC) ウィンドウで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="90ad2-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="90ad2-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="90ad2-152">コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="90ad2-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="90ad2-153">`MyKeysContext` は、 `DbContext` 前のコードサンプルで定義されているです。</span><span class="sxs-lookup"><span data-stu-id="90ad2-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="90ad2-154">を別の名前で使用している場合は `DbContext` 、 `DbContext` の名前に置き換え `MyKeysContext` ます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="90ad2-155">`DataProtectionKeys`クラス/エンティティは、次の表に示す構造を採用しています。</span><span class="sxs-lookup"><span data-stu-id="90ad2-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="90ad2-156">プロパティ/フィールド</span><span class="sxs-lookup"><span data-stu-id="90ad2-156">Property/Field</span></span> | <span data-ttu-id="90ad2-157">CLR 型</span><span class="sxs-lookup"><span data-stu-id="90ad2-157">CLR Type</span></span> | <span data-ttu-id="90ad2-158">SQL 型</span><span class="sxs-lookup"><span data-stu-id="90ad2-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="90ad2-159">`int`、PK、 `IDENTITY(1,1)` 、null 以外</span><span class="sxs-lookup"><span data-stu-id="90ad2-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="90ad2-160">`nvarchar(MAX)`、null</span><span class="sxs-lookup"><span data-stu-id="90ad2-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="90ad2-161">`nvarchar(MAX)`、null</span><span class="sxs-lookup"><span data-stu-id="90ad2-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="90ad2-162">カスタムキーリポジトリ</span><span class="sxs-lookup"><span data-stu-id="90ad2-162">Custom key repository</span></span>

<span data-ttu-id="90ad2-163">インボックス機構が適切でない場合、開発者はカスタム [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)を提供することで、独自のキー永続化メカニズムを指定できます。</span><span class="sxs-lookup"><span data-stu-id="90ad2-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
