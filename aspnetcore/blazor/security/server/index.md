---
title: ASP.NET Core Blazor Server アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402313"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="ccb41-103">ASP.NET Core Blazor Server アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="ccb41-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="ccb41-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ccb41-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor Server<span data-ttu-id="ccb41-105"> アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-105"> apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="ccb41-106">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="ccb41-107">この概要に含まれるトピックは、Blazor Server に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="ccb41-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="ccb41-108"> プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="ccb41-108"> project template</span></span>

<span data-ttu-id="ccb41-109">Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ccb41-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccb41-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ccb41-111">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ccb41-112">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ccb41-113">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ccb41-114">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="ccb41-114">**No Authentication**</span></span>
* <span data-ttu-id="ccb41-115">**個人のユーザー アカウント**: ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="ccb41-116">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="ccb41-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ccb41-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="ccb41-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ccb41-118">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="ccb41-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="ccb41-119">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="ccb41-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ccb41-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ccb41-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ccb41-121">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ccb41-122">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ccb41-123">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="ccb41-123">Authentication mechanism</span></span> | <span data-ttu-id="ccb41-124">説明</span><span class="sxs-lookup"><span data-stu-id="ccb41-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ccb41-125">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="ccb41-125">`None` (default)</span></span>         | <span data-ttu-id="ccb41-126">認証なし</span><span class="sxs-lookup"><span data-stu-id="ccb41-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ccb41-127">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="ccb41-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ccb41-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="ccb41-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ccb41-129">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ccb41-130">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ccb41-131">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-131">Windows Authentication</span></span> |

<span data-ttu-id="ccb41-132">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ccb41-133">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-133">Create a folder for the project.</span></span>
* <span data-ttu-id="ccb41-134">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-134">Name the project.</span></span>

<span data-ttu-id="ccb41-135">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ccb41-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ccb41-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ccb41-137"><xref:blazor/get-started> の記事に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="ccb41-138">**[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ccb41-139">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ccb41-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ccb41-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ccb41-141">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/get-started> の記事に記載されている .NET Core CLI のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ccb41-142">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ccb41-143">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="ccb41-143">Authentication mechanism</span></span> | <span data-ttu-id="ccb41-144">説明</span><span class="sxs-lookup"><span data-stu-id="ccb41-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ccb41-145">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="ccb41-145">`None` (default)</span></span>         | <span data-ttu-id="ccb41-146">認証なし</span><span class="sxs-lookup"><span data-stu-id="ccb41-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ccb41-147">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="ccb41-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ccb41-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="ccb41-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ccb41-149">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ccb41-150">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ccb41-151">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="ccb41-151">Windows Authentication</span></span> |

<span data-ttu-id="ccb41-152">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ccb41-153">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="ccb41-153">Create a folder for the project.</span></span>
* <span data-ttu-id="ccb41-154">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ccb41-154">Name the project.</span></span>

<span data-ttu-id="ccb41-155">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccb41-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="ccb41-156">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="ccb41-156">Scaffold Identity</span></span>

<span data-ttu-id="ccb41-157">Identity を Blazor Server プロジェクトにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ccb41-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="ccb41-158">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="ccb41-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="ccb41-159">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="ccb41-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
