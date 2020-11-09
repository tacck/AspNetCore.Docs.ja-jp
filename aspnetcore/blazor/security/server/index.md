---
title: 'ASP.NET Core Blazor Server アプリをセキュリティで保護する'
author: guardrex
description: 'Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055478"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="c887f-103">ASP.NET Core Blazor Server アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="c887f-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="c887f-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c887f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c887f-105">Blazor Server アプリは、ASP.NET Core アプリと同じ方法でセキュリティが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c887f-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="c887f-106">詳細については、<xref:security/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c887f-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="c887f-107">この概要に含まれるトピックは、Blazor Server に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="c887f-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="c887f-108">Blazor Server プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="c887f-108">Blazor Server project template</span></span>

<span data-ttu-id="c887f-109">Blazor Server プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c887f-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c887f-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c887f-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c887f-111">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="c887f-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c887f-112">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor Server アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c887f-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

<span data-ttu-id="c887f-113">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c887f-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c887f-114">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="c887f-114">**No Authentication**</span></span>
* <span data-ttu-id="c887f-115">**個人のユーザー アカウント** : ユーザーアカウントは次のように格納できます。</span><span class="sxs-lookup"><span data-stu-id="c887f-115">**Individual User Accounts** : User accounts can be stored:</span></span>
  * <span data-ttu-id="c887f-116">ASP.NET Core の [Identity](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="c887f-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c887f-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="c887f-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c887f-118">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="c887f-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="c887f-119">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="c887f-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c887f-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c887f-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c887f-121">認証メカニズムを使用して新しい Blazor Server プロジェクトを作成するには、<xref:blazor/tooling> の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="c887f-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c887f-122">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="c887f-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c887f-123">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="c887f-123">Authentication mechanism</span></span> | <span data-ttu-id="c887f-124">説明</span><span class="sxs-lookup"><span data-stu-id="c887f-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c887f-125">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="c887f-125">`None` (default)</span></span>         | <span data-ttu-id="c887f-126">認証なし</span><span class="sxs-lookup"><span data-stu-id="c887f-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c887f-127">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="c887f-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c887f-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="c887f-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c887f-129">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="c887f-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c887f-130">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="c887f-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c887f-131">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="c887f-131">Windows Authentication</span></span> |

<span data-ttu-id="c887f-132">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c887f-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c887f-133">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c887f-133">Create a folder for the project.</span></span>
* <span data-ttu-id="c887f-134">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c887f-134">Name the project.</span></span>

<span data-ttu-id="c887f-135">詳細については、.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c887f-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c887f-136">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c887f-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c887f-137"><xref:blazor/tooling> に記載されている Visual Studio for Mac のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="c887f-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="c887f-138">**[新しい Blazor Server アプリの構成]** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c887f-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c887f-139">ASP.NET Core Identity を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c887f-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c887f-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c887f-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c887f-141">コマンド シェルで次のコマンドを使用し、認証メカニズムを使って新しい Blazor Server プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="c887f-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c887f-142">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="c887f-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c887f-143">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="c887f-143">Authentication mechanism</span></span> | <span data-ttu-id="c887f-144">説明</span><span class="sxs-lookup"><span data-stu-id="c887f-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c887f-145">`None` (既定値)</span><span class="sxs-lookup"><span data-stu-id="c887f-145">`None` (default)</span></span>         | <span data-ttu-id="c887f-146">認証なし</span><span class="sxs-lookup"><span data-stu-id="c887f-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c887f-147">ASP.NET Core Identity を使用してアプリに格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="c887f-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c887f-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー</span><span class="sxs-lookup"><span data-stu-id="c887f-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c887f-149">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="c887f-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c887f-150">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="c887f-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c887f-151">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="c887f-151">Windows Authentication</span></span> |

<span data-ttu-id="c887f-152">`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c887f-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c887f-153">プロジェクトのフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c887f-153">Create a folder for the project.</span></span>
* <span data-ttu-id="c887f-154">プロジェクトに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="c887f-154">Name the project.</span></span>

<span data-ttu-id="c887f-155">詳細情報:</span><span class="sxs-lookup"><span data-stu-id="c887f-155">For more information:</span></span>

* <span data-ttu-id="c887f-156">.NET Core ガイドの [`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c887f-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="c887f-157">コマンド シェルで Blazor Server テンプレート (`blazorserver`) のヘルプ コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c887f-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="c887f-158">スキャフォールディング Identity</span><span class="sxs-lookup"><span data-stu-id="c887f-158">Scaffold Identity</span></span>

<span data-ttu-id="c887f-159">Identity を Blazor Server プロジェクトにスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="c887f-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="c887f-160">[既存の承認がありません](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c887f-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="c887f-161">[承認があります](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c887f-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c887f-162">その他の資料</span><span class="sxs-lookup"><span data-stu-id="c887f-162">Additional resources</span></span>

* [<span data-ttu-id="c887f-163">クイック スタート:Microsoft でのサインインを ASP.NET Core Web アプリに追加する</span><span class="sxs-lookup"><span data-stu-id="c887f-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="c887f-164">クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する</span><span class="sxs-lookup"><span data-stu-id="c887f-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
