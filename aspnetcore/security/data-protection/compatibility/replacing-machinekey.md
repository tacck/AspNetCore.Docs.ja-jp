---
title: ASP.NET Core の ASP.NET machineKey を置き換える
author: rick-anderson
description: ASP.NET の machineKey を置き換えて、新しいより安全なデータ保護システムを使用できるようにする方法について説明します。
ms.author: riande
ms.date: 04/06/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053060"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="eb8f5-103">ASP.NET Core の ASP.NET machineKey を置き換える</span><span class="sxs-lookup"><span data-stu-id="eb8f5-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="eb8f5-104">`<machineKey>`ASP.NET の要素の実装[は置き換えることが](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)できます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="eb8f5-105">これにより、ASP.NET 暗号化ルーチンのほとんどの呼び出しは、新しいデータ保護システムを含む代替のデータ保護メカニズムを通じてルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="eb8f5-106">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="eb8f5-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="eb8f5-107">新しいデータ保護システムは、.NET 4.5.1 以降を対象とする既存の ASP.NET アプリケーションにのみインストールできます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="eb8f5-108">アプリケーションが .NET 4.5 以下を対象としている場合、インストールは失敗します。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="eb8f5-109">新しいデータ保護システムを既存の ASP.NET 4.5.1 + プロジェクトにインストールするには、パッケージ Microsoft.AspNetCore.DataProtection.SystemWeb にインストールします。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="eb8f5-110">これにより、 [既定の構成](xref:security/data-protection/configuration/default-settings) 設定を使用してデータ保護システムがインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="eb8f5-111">パッケージをインストールすると、 *Web.config* に行が挿入されます。これにより、フォーム認証、ビューステート、および ASP.NET の呼び出しなど、 [ほとんどの暗号化操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)に使用するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="eb8f5-112">挿入された行は次のように読み取られます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="eb8f5-113">新しいデータ保護システムがアクティブであるかどうかを確認するには、のようなフィールド `__VIEWSTATE` を調べます。次の例のように、"CfDJ8" で始まる必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="eb8f5-114">"CfDJ8" は、データ保護システムによって保護されているペイロードを識別するマジック "09 F0 C9 F0" ヘッダーの base64 表現です。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="eb8f5-115">パッケージの構成</span><span class="sxs-lookup"><span data-stu-id="eb8f5-115">Package configuration</span></span>

<span data-ttu-id="eb8f5-116">データ保護システムは、既定のゼロセットアップ構成を使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="eb8f5-117">ただし、既定ではキーはローカルファイルシステムに保存されるため、ファームに配置されているアプリケーションでは機能しません。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="eb8f5-118">これを解決するには、DataProtectionStartup にサブクラスを作成し、その ConfigureServices メソッドをオーバーライドする型を作成して、構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="eb8f5-119">次に示すのは、キーが永続化される場所と保存時に暗号化する方法の両方を構成したカスタムデータ保護のスタートアップの種類の例です。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="eb8f5-120">また、独自のアプリケーション名を指定することで、既定のアプリ分離ポリシーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="eb8f5-121">`<machineKey applicationName="my-app" ... />`SetApplicationName への明示的な呼び出しの代わりにを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="eb8f5-122">これは、構成するすべてのユーザーがアプリケーション名を設定していた場合に、開発者が DataProtectionStartup 派生型を作成する必要がないようにするための便利なメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="eb8f5-123">このカスタム構成を有効にするには、[Web.config に戻り、 `<appSettings>` パッケージのインストールによって構成ファイルに追加された要素を探します。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="eb8f5-124">次のマークアップが表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="eb8f5-125">空の値に、先ほど作成した DataProtectionStartup 派生型のアセンブリ修飾名を入力します。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="eb8f5-126">アプリケーションの名前が DataProtectionDemo の場合、これは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="eb8f5-127">これで、新しく構成されたデータ保護システムをアプリケーション内で使用する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="eb8f5-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
