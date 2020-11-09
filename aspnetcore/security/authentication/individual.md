---
title: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル
author: rick-anderson
description: 個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づいて、記事を発見します。
ms.author: riande
ms.date: 12/11/2019
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
uid: security/authentication/individual
ms.openlocfilehash: 656006396de120b7feae6f2e08b5dad3b5a170b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053346"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="b341c-103">個々のユーザーアカウントで作成された ASP.NET Core プロジェクトに基づくアーティクル</span><span class="sxs-lookup"><span data-stu-id="b341c-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="b341c-104">ASP.NET Core Identity は、Visual Studio の [個別のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b341c-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="b341c-105">認証テンプレートは、.NET Core CLI で使用でき `-au Individual` ます。</span><span class="sxs-lookup"><span data-stu-id="b341c-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="b341c-106">Web API 認証については、 [この GitHub の問題](https://github.com/dotnet/AspNetCore/issues/5833) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b341c-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="b341c-107">[認証なし]</span><span class="sxs-lookup"><span data-stu-id="b341c-107">No Authentication</span></span>

<span data-ttu-id="b341c-108">.NET Core CLI では、オプションを指定して認証を指定し `-au` ます。</span><span class="sxs-lookup"><span data-stu-id="b341c-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="b341c-109">Visual Studio では、新しい web アプリケーションで [ **認証の変更** ] ダイアログを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b341c-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="b341c-110">Visual Studio での新しい web アプリの既定値は、認証され **ません** 。</span><span class="sxs-lookup"><span data-stu-id="b341c-110">The default for new web apps in Visual Studio is **No Authentication** .</span></span>

<span data-ttu-id="b341c-111">認証なしで作成されたプロジェクト:</span><span class="sxs-lookup"><span data-stu-id="b341c-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="b341c-112">サインインおよびサインアウトするための web ページと UI を含めないでください。</span><span class="sxs-lookup"><span data-stu-id="b341c-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="b341c-113">認証コードを含めないでください。</span><span class="sxs-lookup"><span data-stu-id="b341c-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="b341c-114">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="b341c-114">Windows Authentication</span></span>

<span data-ttu-id="b341c-115">Windows 認証は、オプションを使用して .NET Core CLI の新しい web アプリに対して指定され `-au Windows` ます。</span><span class="sxs-lookup"><span data-stu-id="b341c-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="b341c-116">Visual Studio では、[ **認証の変更** ] ダイアログボックスに **Windows 認証** オプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b341c-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="b341c-117">[Windows 認証] が選択されている場合、アプリは [Windows 認証 IIS モジュール](xref:host-and-deploy/iis/modules)を使用するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="b341c-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="b341c-118">Windows 認証は、イントラネット web サイトを対象としています。</span><span class="sxs-lookup"><span data-stu-id="b341c-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="b341c-119">dotnet 新しい webapp 認証オプション</span><span class="sxs-lookup"><span data-stu-id="b341c-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="b341c-120">次の表は、新しい web アプリで使用できる認証オプションを示しています。</span><span class="sxs-lookup"><span data-stu-id="b341c-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="b341c-121">オプション</span><span class="sxs-lookup"><span data-stu-id="b341c-121">Option</span></span> | <span data-ttu-id="b341c-122">認証の種類</span><span class="sxs-lookup"><span data-stu-id="b341c-122">Type of authentication</span></span> | <span data-ttu-id="b341c-123">詳細情報のリンク</span><span class="sxs-lookup"><span data-stu-id="b341c-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="b341c-124">なし</span><span class="sxs-lookup"><span data-stu-id="b341c-124">None</span></span>            |  <span data-ttu-id="b341c-125">認証なし</span><span class="sxs-lookup"><span data-stu-id="b341c-125">No authentication</span></span> | | 
| <span data-ttu-id="b341c-126">個人</span><span class="sxs-lookup"><span data-stu-id="b341c-126">Individual</span></span>      |  <span data-ttu-id="b341c-127">個々の認証</span><span class="sxs-lookup"><span data-stu-id="b341c-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="b341c-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="b341c-128">IndividualB2C</span></span>   |  <span data-ttu-id="b341c-129">Azure AD B2C を使用したクラウドホストの個々の認証</span><span class="sxs-lookup"><span data-stu-id="b341c-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="b341c-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="b341c-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="b341c-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="b341c-131">SingleOrg</span></span>       |  <span data-ttu-id="b341c-132">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="b341c-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="b341c-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="b341c-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="b341c-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="b341c-134">MultiOrg</span></span>        |  <span data-ttu-id="b341c-135">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="b341c-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="b341c-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="b341c-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="b341c-137">Windows</span><span class="sxs-lookup"><span data-stu-id="b341c-137">Windows</span></span>         |  <span data-ttu-id="b341c-138">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="b341c-138">Windows authentication</span></span> | [<span data-ttu-id="b341c-139">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="b341c-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="b341c-140">Visual Studio の新しい webapp 認証オプション</span><span class="sxs-lookup"><span data-stu-id="b341c-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="b341c-141">次の表は、Visual Studio で新しい web アプリを作成するときに使用できる認証オプションを示しています。</span><span class="sxs-lookup"><span data-stu-id="b341c-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="b341c-142">オプション</span><span class="sxs-lookup"><span data-stu-id="b341c-142">Option</span></span> | <span data-ttu-id="b341c-143">認証の種類</span><span class="sxs-lookup"><span data-stu-id="b341c-143">Type of authentication</span></span> | <span data-ttu-id="b341c-144">詳細情報のリンク</span><span class="sxs-lookup"><span data-stu-id="b341c-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="b341c-145">なし</span><span class="sxs-lookup"><span data-stu-id="b341c-145">None</span></span>            |  <span data-ttu-id="b341c-146">認証なし</span><span class="sxs-lookup"><span data-stu-id="b341c-146">No authentication</span></span> | | 
| <span data-ttu-id="b341c-147">個々のユーザーアカウント/アプリ内のユーザーアカウントを格納する</span><span class="sxs-lookup"><span data-stu-id="b341c-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="b341c-148">個々の認証</span><span class="sxs-lookup"><span data-stu-id="b341c-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="b341c-149">個々のユーザーアカウント/クラウド内の既存のユーザーストアに接続する</span><span class="sxs-lookup"><span data-stu-id="b341c-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="b341c-150">Azure AD B2C を使用したクラウドホストの個々の認証</span><span class="sxs-lookup"><span data-stu-id="b341c-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="b341c-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="b341c-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="b341c-152">職場または学校のクラウド/単一組織</span><span class="sxs-lookup"><span data-stu-id="b341c-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="b341c-153">単一のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="b341c-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="b341c-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="b341c-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="b341c-155">職場または学校のクラウド/複数の組織</span><span class="sxs-lookup"><span data-stu-id="b341c-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="b341c-156">複数のテナントに対する組織認証</span><span class="sxs-lookup"><span data-stu-id="b341c-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="b341c-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="b341c-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="b341c-158">Windows</span><span class="sxs-lookup"><span data-stu-id="b341c-158">Windows</span></span>         |  <span data-ttu-id="b341c-159">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="b341c-159">Windows authentication</span></span> | [<span data-ttu-id="b341c-160">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="b341c-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="b341c-161">その他の資料</span><span class="sxs-lookup"><span data-stu-id="b341c-161">Additional resources</span></span>

<span data-ttu-id="b341c-162">次の記事では、個々のユーザーアカウントを使用する ASP.NET Core テンプレートで生成されたコードを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b341c-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="b341c-163">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="b341c-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b341c-164">承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b341c-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
