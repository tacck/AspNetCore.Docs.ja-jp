---
title: ASP.NET Core Blazor サーバー アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206330"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="f595d-103">ASP.NET Core Blazor サーバー アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="f595d-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="f595d-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f595d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="f595d-105">Blazor サーバー プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="f595d-105">Blazor Server project template</span></span>

<span data-ttu-id="f595d-106">Blazor サーバー プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="f595d-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f595d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f595d-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f595d-108">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f595d-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="f595d-109">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f595d-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="f595d-110">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f595d-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="f595d-111">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="f595d-111">**No Authentication**</span></span>
* <span data-ttu-id="f595d-112">**個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。</span><span class="sxs-lookup"><span data-stu-id="f595d-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="f595d-113">ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="f595d-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="f595d-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="f595d-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="f595d-115">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="f595d-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="f595d-116">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="f595d-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f595d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f595d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f595d-118">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f595d-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f595d-119">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="f595d-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f595d-120">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="f595d-120">Authentication mechanism</span></span> | <span data-ttu-id="f595d-121">説明</span><span class="sxs-lookup"><span data-stu-id="f595d-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f595d-122">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="f595d-122">`None` (default)</span></span>         | <span data-ttu-id="f595d-123">認証なし</span><span class="sxs-lookup"><span data-stu-id="f595d-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f595d-124">ASP.NET Core ID を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="f595d-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f595d-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="f595d-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f595d-126">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="f595d-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f595d-127">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="f595d-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f595d-128">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="f595d-128">Windows Authentication</span></span> |

<span data-ttu-id="f595d-129">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f595d-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f595d-130">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="f595d-130">Create a folder for the project.</span></span>
* <span data-ttu-id="f595d-131">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="f595d-131">Name the project.</span></span>

<span data-ttu-id="f595d-132">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f595d-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f595d-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f595d-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f595d-134"><xref:blazor/get-started> の記事に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f595d-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="f595d-135">**[Configure your new Blazor Server App]\(新しい Blazor サーバー アプリの構成\)** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f595d-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f595d-136">ASP.NET Core ID を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f595d-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f595d-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f595d-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f595d-138">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事に記載されている .NET Core CLI のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f595d-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f595d-139">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="f595d-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f595d-140">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="f595d-140">Authentication mechanism</span></span> | <span data-ttu-id="f595d-141">説明</span><span class="sxs-lookup"><span data-stu-id="f595d-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f595d-142">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="f595d-142">`None` (default)</span></span>         | <span data-ttu-id="f595d-143">認証なし</span><span class="sxs-lookup"><span data-stu-id="f595d-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f595d-144">ASP.NET Core ID を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="f595d-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f595d-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="f595d-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f595d-146">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="f595d-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f595d-147">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="f595d-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f595d-148">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="f595d-148">Windows Authentication</span></span> |

<span data-ttu-id="f595d-149">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f595d-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f595d-150">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="f595d-150">Create a folder for the project.</span></span>
* <span data-ttu-id="f595d-151">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="f595d-151">Name the project.</span></span>

<span data-ttu-id="f595d-152">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f595d-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
