---
title: ASP.NET Core Blazor Server アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 4dc9040b9410304eb33e5df7c47db2f9a42152d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013997"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="3d8c8-103">ASP.NET Core Blazor Server アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="3d8c8-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="3d8c8-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d8c8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3d8c8-105">Blazor Server アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="3d8c8-106">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="3d8c8-107">この概要に含まれるトピックは、Blazor Server に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="3d8c8-108">Blazor Server プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="3d8c8-108">Blazor Server project template</span></span>

<span data-ttu-id="3d8c8-109">Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3d8c8-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d8c8-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3d8c8-111">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="3d8c8-112">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="3d8c8-113">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="3d8c8-114">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="3d8c8-114">**No Authentication**</span></span>
* <span data-ttu-id="3d8c8-115">**個人のユーザー アカウント**: ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="3d8c8-116">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="3d8c8-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="3d8c8-118">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="3d8c8-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="3d8c8-119">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="3d8c8-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3d8c8-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3d8c8-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3d8c8-121">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3d8c8-122">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3d8c8-123">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="3d8c8-123">Authentication mechanism</span></span> | <span data-ttu-id="3d8c8-124">説明</span><span class="sxs-lookup"><span data-stu-id="3d8c8-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3d8c8-125">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="3d8c8-125">`None` (default)</span></span>         | <span data-ttu-id="3d8c8-126">認証なし</span><span class="sxs-lookup"><span data-stu-id="3d8c8-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3d8c8-127">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="3d8c8-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3d8c8-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="3d8c8-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3d8c8-129">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3d8c8-130">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3d8c8-131">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-131">Windows Authentication</span></span> |

<span data-ttu-id="3d8c8-132">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3d8c8-133">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-133">Create a folder for the project.</span></span>
* <span data-ttu-id="3d8c8-134">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-134">Name the project.</span></span>

<span data-ttu-id="3d8c8-135">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3d8c8-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3d8c8-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3d8c8-137"><xref:blazor/tooling> に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="3d8c8-138">**[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="3d8c8-139">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3d8c8-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3d8c8-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3d8c8-141">コマンド シェルで次のコマンドを使用し、認証メカニズムを使って新しい Blazor Server プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3d8c8-142">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3d8c8-143">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="3d8c8-143">Authentication mechanism</span></span> | <span data-ttu-id="3d8c8-144">説明</span><span class="sxs-lookup"><span data-stu-id="3d8c8-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3d8c8-145">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="3d8c8-145">`None` (default)</span></span>         | <span data-ttu-id="3d8c8-146">認証なし</span><span class="sxs-lookup"><span data-stu-id="3d8c8-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3d8c8-147">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="3d8c8-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3d8c8-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="3d8c8-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3d8c8-149">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3d8c8-150">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3d8c8-151">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="3d8c8-151">Windows Authentication</span></span> |

<span data-ttu-id="3d8c8-152">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3d8c8-153">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-153">Create a folder for the project.</span></span>
* <span data-ttu-id="3d8c8-154">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-154">Name the project.</span></span>

<span data-ttu-id="3d8c8-155">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="3d8c8-156">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="3d8c8-156">Scaffold Identity</span></span>

<span data-ttu-id="3d8c8-157">Identity を Blazor Server プロジェクトにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="3d8c8-158">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="3d8c8-159">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="3d8c8-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
