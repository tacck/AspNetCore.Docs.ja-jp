---
title: ASP.NET Core Blazor サーバー アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454637"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="db072-103">ASP.NET Core Blazor サーバー アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="db072-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="db072-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db072-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="db072-105"> サーバー プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="db072-105"> Server project template</span></span>

<span data-ttu-id="db072-106">Blazor サーバー プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="db072-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="db072-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db072-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="db072-108">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="db072-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="db072-109">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="db072-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="db072-110">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="db072-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="db072-111">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="db072-111">**No Authentication**</span></span>
* <span data-ttu-id="db072-112">**個人のユーザー アカウント**: ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="db072-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="db072-113">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="db072-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="db072-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="db072-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="db072-115">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="db072-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="db072-116">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="db072-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="db072-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db072-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="db072-118">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="db072-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="db072-119">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="db072-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="db072-120">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="db072-120">Authentication mechanism</span></span> | <span data-ttu-id="db072-121">説明</span><span class="sxs-lookup"><span data-stu-id="db072-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="db072-122">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="db072-122">`None` (default)</span></span>         | <span data-ttu-id="db072-123">認証なし</span><span class="sxs-lookup"><span data-stu-id="db072-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="db072-124">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="db072-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="db072-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="db072-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="db072-126">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="db072-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="db072-127">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="db072-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="db072-128">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="db072-128">Windows Authentication</span></span> |

<span data-ttu-id="db072-129">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="db072-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="db072-130">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="db072-130">Create a folder for the project.</span></span>
* <span data-ttu-id="db072-131">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="db072-131">Name the project.</span></span>

<span data-ttu-id="db072-132">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db072-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="db072-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="db072-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="db072-134"><xref:blazor/get-started> の記事に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="db072-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="db072-135">**[Configure your new Blazor Server App]\(新しい Blazor サーバー アプリの構成\)** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="db072-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="db072-136">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="db072-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="db072-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="db072-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="db072-138">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事に記載されている .NET Core CLI のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="db072-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="db072-139">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="db072-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="db072-140">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="db072-140">Authentication mechanism</span></span> | <span data-ttu-id="db072-141">説明</span><span class="sxs-lookup"><span data-stu-id="db072-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="db072-142">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="db072-142">`None` (default)</span></span>         | <span data-ttu-id="db072-143">認証なし</span><span class="sxs-lookup"><span data-stu-id="db072-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="db072-144">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="db072-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="db072-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="db072-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="db072-146">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="db072-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="db072-147">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="db072-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="db072-148">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="db072-148">Windows Authentication</span></span> |

<span data-ttu-id="db072-149">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="db072-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="db072-150">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="db072-150">Create a folder for the project.</span></span>
* <span data-ttu-id="db072-151">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="db072-151">Name the project.</span></span>

<span data-ttu-id="db072-152">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db072-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="db072-153">既存のアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="db072-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="db072-154"> サーバー アプリは、ASP.NET Core アプリと同じ方法でセキュリティ用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="db072-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="db072-155">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db072-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="db072-156">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="db072-156">Scaffold Identity</span></span>

<span data-ttu-id="db072-157">スキャフォールディングを Blazor サーバープロジェクトに Identity します。</span><span class="sxs-lookup"><span data-stu-id="db072-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="db072-158">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="db072-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="db072-159">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="db072-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
