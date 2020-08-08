---
title: ASP.NET Core プロジェクトでのユーザーデータの追加、ダウンロード、および削除 Identity
author: rick-anderson
description: ASP.NET Core プロジェクト内のにカスタムユーザーデータを追加する方法について説明 Identity します。 GDPR ごとのデータを削除します。
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: d65974e9ff8e2f5be52ab79b063ed9d2dca557ea
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020861"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="a347c-104">カスタムユーザーデータを Identity ASP.NET Core プロジェクトに追加、ダウンロード、および削除する</span><span class="sxs-lookup"><span data-stu-id="a347c-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="a347c-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a347c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a347c-106">この記事では、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a347c-106">This article shows how to:</span></span>

* <span data-ttu-id="a347c-107">カスタムユーザーデータを ASP.NET Core web アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="a347c-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="a347c-108">カスタムユーザーデータモデルを属性でマークして、 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 自動的にダウンロードおよび削除できるようにします。</span><span class="sxs-lookup"><span data-stu-id="a347c-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="a347c-109">データをダウンロードして削除できるようにすると、 [GDPR](xref:security/gdpr)の要件を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="a347c-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="a347c-110">プロジェクトサンプルは、ページ web アプリから作成され Razor ますが、手順は ASP.NET CORE MVC web アプリの場合と似ています。</span><span class="sxs-lookup"><span data-stu-id="a347c-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a347c-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a347c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a347c-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a347c-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="a347c-113">Web アプリを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="a347c-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a347c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a347c-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="a347c-115">Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="a347c-116">[ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a347c-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="a347c-117">**ASP.NET Core Web アプリケーション**の選択- > **OK**</span><span class="sxs-lookup"><span data-stu-id="a347c-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="a347c-118">ドロップダウンで**ASP.NET Core 3.0**を選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="a347c-119">**Web アプリケーション**の選択- > **OK**</span><span class="sxs-lookup"><span data-stu-id="a347c-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="a347c-120">プロジェクトをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="a347c-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="a347c-121">Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="a347c-122">[ダウンロードするサンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致させる場合は、プロジェクトに**WebApp1**という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a347c-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="a347c-123">**ASP.NET Core Web アプリケーション**の選択- > **OK**</span><span class="sxs-lookup"><span data-stu-id="a347c-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="a347c-124">ドロップダウンで**ASP.NET Core 2.2**を選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="a347c-125">**Web アプリケーション**の選択- > **OK**</span><span class="sxs-lookup"><span data-stu-id="a347c-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="a347c-126">プロジェクトをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="a347c-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="a347c-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a347c-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="a347c-128">Scaffolder を実行します。 Identity</span><span class="sxs-lookup"><span data-stu-id="a347c-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a347c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a347c-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a347c-130">**ソリューションエクスプローラー**で、プロジェクトを右クリックし、[ **Add**  >  **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a347c-131">[**スキャフォールディングの追加**] ダイアログボックスの左ペインで、[追加] を選択し **Identity**  >  **Add**ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a347c-132">[\*\*追加 Identity \*\* ] ダイアログで、次のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="a347c-133">既存のレイアウト _Layout ファイルを選択し*ます。*</span><span class="sxs-lookup"><span data-stu-id="a347c-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="a347c-134">上書きする以下のファイルを選択してください:</span><span class="sxs-lookup"><span data-stu-id="a347c-134">Select the following files to override:</span></span>
    * <span data-ttu-id="a347c-135">**アカウント/登録**</span><span class="sxs-lookup"><span data-stu-id="a347c-135">**Account/Register**</span></span>
    * <span data-ttu-id="a347c-136">**アカウント/管理/インデックス**</span><span class="sxs-lookup"><span data-stu-id="a347c-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="a347c-137">**+** 新しい**データコンテキストクラス**を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a347c-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a347c-138">型 (プロジェクトの名前が**WebApp1**の場合は**WebApp1Context** ) をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="a347c-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="a347c-139">**+** 新しい**ユーザークラス**を作成するには、このボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="a347c-140">型を受け入れます (プロジェクトの名前が**WebApp1**の場合は**WebApp1User** ) >**追加**] を使用します。</span><span class="sxs-lookup"><span data-stu-id="a347c-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="a347c-141">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a347c-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a347c-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a347c-143">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="a347c-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a347c-144">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="a347c-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="a347c-145">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a347c-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="a347c-146">次のコマンドを実行して、scaffolder オプションの一覧を表示し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="a347c-147">プロジェクトフォルダーで、scaffolder を実行し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="a347c-148">[移行、UseAuthentication、および layout](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="a347c-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="a347c-149">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="a347c-150">`UseAuthentication` に `Startup.Configure` を追加します。</span><span class="sxs-lookup"><span data-stu-id="a347c-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="a347c-151">`<partial name="_LoginPartial" />`レイアウトファイルにを追加します。</span><span class="sxs-lookup"><span data-stu-id="a347c-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="a347c-152">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="a347c-152">Test the app:</span></span>
  * <span data-ttu-id="a347c-153">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="a347c-153">Register a user</span></span>
  * <span data-ttu-id="a347c-154">[**ログアウト**] リンクの横にある新しいユーザー名を選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="a347c-155">場合によっては、ウィンドウを展開するか、ナビゲーションバーのアイコンを選択して、ユーザー名とその他のリンクを表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a347c-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="a347c-156">[ **Personal Data** ] タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="a347c-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="a347c-157">[**ダウンロード**] ボタンを選択し、ファイルの*PersonalData.js*を調べます。</span><span class="sxs-lookup"><span data-stu-id="a347c-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="a347c-158">[**削除**] ボタンをテストします。これにより、ログオンしているユーザーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="a347c-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="a347c-159">DB にカスタムユーザーデータを追加する Identity</span><span class="sxs-lookup"><span data-stu-id="a347c-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="a347c-160">`IdentityUser`カスタムプロパティを使用して、派生クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="a347c-161">プロジェクトに WebApp1 という名前を付けた場合、ファイルの名前は*Areas/ Identity /Data/WebApp1User.cs*になります。</span><span class="sxs-lookup"><span data-stu-id="a347c-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="a347c-162">次のコードを使用して、ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="a347c-163">プロパティ[は、次のよう](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)になります。</span><span class="sxs-lookup"><span data-stu-id="a347c-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="a347c-164">*区分/ Identity /Pages/Account/Manage/DeletePersonalData.cshtml*ページがを呼び出したときに削除され Razor `UserManager.Delete` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="a347c-165">ダウンロードされたデータには、[区分]、[ページ]、*および [ Identity Manage/Download] の各*ページで含まれ Razor ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="a347c-166">Account/Manage/Index. cshtml ページを更新する</span><span class="sxs-lookup"><span data-stu-id="a347c-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="a347c-167">次の `InputModel` 強調表示されたコードを使用して、*区分/ Identity /Pages/Account/Manage/Index.cshtml.cs*のを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="a347c-168">次の強調表示されているマークアップを使用して、\*区分//////////また Identity \*は更新プログラムを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="a347c-169">次の強調表示されているマークアップを使用して、\*区分//////////また Identity \*は更新プログラムを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="a347c-170">Account/Register. cshtml ページを更新する</span><span class="sxs-lookup"><span data-stu-id="a347c-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="a347c-171">次の `InputModel` 強調表示されたコードを使用して、*区分/ Identity /Pages/Account/Register.cshtml.cs*のを更新します。</span><span class="sxs-lookup"><span data-stu-id="a347c-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="a347c-172">次の強調表示されたマークアップを使用して、\*区分//[///////////////また Identity \*はの</span><span class="sxs-lookup"><span data-stu-id="a347c-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="a347c-173">次の強調表示されたマークアップを使用して、\*区分//[///////////////また Identity \*はの</span><span class="sxs-lookup"><span data-stu-id="a347c-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="a347c-174">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="a347c-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="a347c-175">カスタムユーザーデータの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="a347c-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a347c-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a347c-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a347c-177">Visual Studio**パッケージマネージャーコンソール**で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a347c-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="a347c-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a347c-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="a347c-179">テストの作成、表示、ダウンロード、カスタムユーザーデータの削除</span><span class="sxs-lookup"><span data-stu-id="a347c-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="a347c-180">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="a347c-180">Test the app:</span></span>

* <span data-ttu-id="a347c-181">新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="a347c-181">Register a new user.</span></span>
* <span data-ttu-id="a347c-182">ページ上のカスタムユーザーデータを表示し `/Identity/Account/Manage` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="a347c-183">ページからユーザーの個人データをダウンロードして表示し `/Identity/Account/Manage/PersonalData` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="a347c-184">IdentityIUserClaimsPrincipalFactory を使用してにクレームを追加する<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="a347c-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="a347c-185">このセクションは、前のチュートリアルの拡張機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="a347c-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="a347c-186">チュートリアルを使用してビルドされたアプリに次の手順を適用するには、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/18797)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a347c-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="a347c-187">インターフェイスを使用して、追加の要求を ASP.NET Core に追加することができ Identity `IUserClaimsPrincipalFactory<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="a347c-188">このクラスは、メソッドでアプリに追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a347c-189">次のように、クラスのカスタム実装を追加します。</span><span class="sxs-lookup"><span data-stu-id="a347c-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="a347c-190">デモコードでは、クラスを使用し `ApplicationUser` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="a347c-191">このクラス `IsAdmin` は、追加の要求を追加するために使用されるプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="a347c-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="a347c-192">`AdditionalUserClaimsPrincipalFactory` は、`UserClaimsPrincipalFactory` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="a347c-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="a347c-193">新しいロールクレームがに追加され `ClaimsPrincipal` ます。</span><span class="sxs-lookup"><span data-stu-id="a347c-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="a347c-194">その後、追加の要求をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="a347c-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="a347c-195">ページでは、 Razor `IAuthorizationService` インスタンスを使用して要求値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="a347c-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
