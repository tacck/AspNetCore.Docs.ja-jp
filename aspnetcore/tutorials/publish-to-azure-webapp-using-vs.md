---
title: Visual Studio を使用して Azure に ASP.NET Core アプリを発行する
author: rick-anderson
description: Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060223"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="7e033-103">Visual Studio を使用して Azure に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="7e033-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="7e033-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7e033-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="7e033-105">macOS で作業している場合は、「[Visual Studio for Mac を使用して Azure App Service に Web アプリを発行する](/visualstudio/mac/publish-app-svc?view=vsmac-2019)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e033-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="7e033-106">App Service の配置に関する問題を解決するには、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e033-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="7e033-107">設定</span><span class="sxs-lookup"><span data-stu-id="7e033-107">Set up</span></span>

* <span data-ttu-id="7e033-108">Azure アカウントをお持ちでない場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成します。</span><span class="sxs-lookup"><span data-stu-id="7e033-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="7e033-109">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="7e033-109">Create a web app</span></span>

<span data-ttu-id="7e033-110">Visual Studio のスタート ページで、 **[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![[ファイル] メニュー](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="7e033-112">**[新しいプロジェクト]** ダイアログで次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="7e033-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="7e033-113">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7e033-114">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-114">Select **Next**.</span></span>

![[新しいプロジェクト] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="7e033-116">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="7e033-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="7e033-117">**[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-117">Select **Web Application**.</span></span>
* <span data-ttu-id="7e033-118">[認証] で **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-118">Select **Change** under Authentication.</span></span>

![[新しい ASP.NET Core Web アプリケーション] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="7e033-120">**[認証の変更]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="7e033-121">**[個人のユーザー アカウント]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="7e033-122">**[OK]** を選択して **[新しい ASP.NET Core Web アプリケーション]** に戻り、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create**.</span></span>

![新しい ASP.NET Core Web 認証ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="7e033-124">Visual Studio によってソリューションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="7e033-125">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="7e033-125">Run the app</span></span>

* <span data-ttu-id="7e033-126">Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。</span><span class="sxs-lookup"><span data-stu-id="7e033-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="7e033-127">**[プライバシー]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="7e033-127">Test the **Privacy** link.</span></span>

![Microsoft Edge で開いているローカルホストの Web アプリケーション](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="7e033-129">ユーザーを登録する</span><span class="sxs-lookup"><span data-stu-id="7e033-129">Register a user</span></span>

* <span data-ttu-id="7e033-130">**[登録]** を選択して、新しいユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="7e033-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="7e033-131">架空の電子メール アドレスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7e033-131">You can use a fictitious email address.</span></span> <span data-ttu-id="7e033-132">送信すると、ページに次のエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="7e033-133">*A database operation failed while processing the request./(要求の処理中にデータベースの操作に失敗しました。/)Applying existing migrations for Application DB context may resolve this issue. /(アプリケーション DB コンテキストの既存の移行を適用すると問題が解決する場合があります。/)*</span><span class="sxs-lookup"><span data-stu-id="7e033-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="7e033-134">**[Apply Migrations]/(移行を適用する/)** を選択し、移行が完了したら、ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="7e033-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![要求の処理中にデータベースの操作に失敗しました。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="7e033-137">アプリに、新しいユーザーの登録に使用した電子メールと **[ログアウト]** リンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Microsoft Edge で開いている Web アプリケーション。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="7e033-140">Azure にアプリを配置する</span><span class="sxs-lookup"><span data-stu-id="7e033-140">Deploy the app to Azure</span></span>

<span data-ttu-id="7e033-141">ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="7e033-143">**[発行]** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="7e033-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="7e033-144">**[Azure]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-144">Select **Azure**.</span></span>
* <span data-ttu-id="7e033-145">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-145">Select **Next**.</span></span>

![[発行] ダイアログ](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="7e033-147">**[発行]** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="7e033-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="7e033-148">**[Azure App Service (Linux)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="7e033-149">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-149">Select **Next**.</span></span>

![[発行] ダイアログ: Azure サービスを選択](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="7e033-151">**[発行]** ダイアログで、 **[新しい Azure App Service の作成...]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="7e033-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![[発行] ダイアログ: Azure サービス インスタンスを選択](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="7e033-153">**[App Service の作成]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="7e033-154">**[アプリ名]** 、 **[リソース グループ]** 、 **[App Service プラン]** の各入力フィールドに値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="7e033-155">これらの名前を保持することも、変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="7e033-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="7e033-156">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-156">Select **Create**.</span></span>

![[App Service の作成] ダイアログ](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="7e033-158">作成が完了すると、ダイアログが自動的に閉じられ、 **[発行]** ダイアログに再度フォーカスが移ります。</span><span class="sxs-lookup"><span data-stu-id="7e033-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="7e033-159">作成したばかりの新しいインスタンスが自動的に選択されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="7e033-160">**[完了]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-160">Select **Finish**.</span></span>

![[発行] ダイアログ: App Service インスタンスを選択](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="7e033-162">次に、 **発行プロファイルの概要** ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="7e033-163">Visual Studio によって、このアプリケーションには SQL Server データベースが必要であることが検出されているため、構成するように求められています。</span><span class="sxs-lookup"><span data-stu-id="7e033-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="7e033-164">**[構成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e033-164">Select **Configure**.</span></span>

![発行プロファイルの概要ページ: SQL Server 依存関係の構成](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="7e033-166">**[依存関係の構成]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="7e033-167">**[Azure SQL Database]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="7e033-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-168">Select **Next**.</span></span>

![SQL Server の依存関係の構成ダイアログ](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="7e033-170">**[Azure SQL Database の構成]** ダイアログで **[SQL Database の作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![[Azure SQL Database の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="7e033-172">**Azure SQL Database の作成** が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="7e033-173">**[データベース名]** 、 **[リソース グループ]** 、 **[データベース サーバー]** 、 **[App Service プラン]** の各入力フィールドに値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="7e033-174">これらの値を保持することも、変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="7e033-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="7e033-175">選択した **[データベース サーバー]** の **[データベース管理者のユーザー名]** と **[データベース管理者のパスワード]** を入力します (注: 使用するアカウントには、新しい Azure SQL データベースを作成するために必要なアクセス許可が必要です)。</span><span class="sxs-lookup"><span data-stu-id="7e033-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="7e033-176">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-176">Select **Create**.</span></span>

![新しい [Azure SQL Database] ダイアログ](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="7e033-178">作成が完了すると、ダイアログが自動的に閉じられ、 **[Azure SQL Database の構成]** ダイアログに再度フォーカスが移ります。</span><span class="sxs-lookup"><span data-stu-id="7e033-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="7e033-179">作成したばかりの新しいインスタンスが自動的に選択されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="7e033-180">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-180">Select **Next**.</span></span>

![[Azure SQL Database の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="7e033-182">**[Azure SQL Database の構成]** ダイアログの次の手順で、以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="7e033-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="7e033-183">**[データベース接続のユーザー名]** と **[データベース管理者のパスワード]** フィールドを入力します。</span><span class="sxs-lookup"><span data-stu-id="7e033-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="7e033-184">これらは、アプリケーションが実行時にデータベースに接続するために使用する詳細です。</span><span class="sxs-lookup"><span data-stu-id="7e033-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="7e033-185">ベスト プラクティスとして、前の手順で使用した管理者ユーザー名とパスワードと同じ詳細を使用しないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7e033-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="7e033-186">**[完了]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-186">Select **Finish**.</span></span>

![[Azure SQL Database の構成] ダイアログ、接続文字列の詳細](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="7e033-188">**発行プロファイルの概要** ページで **[設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![発行プロファイルの概要ページ: 設定の編集](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="7e033-190">**[発行]** ダイアログの **[設定]** ページで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="7e033-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="7e033-191">**[データベース]** を展開し、 **[この接続文字列を実行時に使用する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="7e033-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="7e033-192">**[Entity Framework の移行]** を展開し、 **[発行時にこの移行を適用する]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="7e033-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="7e033-193">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-193">Select **Save**.</span></span> <span data-ttu-id="7e033-194">Visual Studio が **[発行]** ダイアログに戻ります。</span><span class="sxs-lookup"><span data-stu-id="7e033-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![[発行] ダイアログ:[設定] パネル](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="7e033-196">**[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e033-196">Click **Publish**.</span></span> <span data-ttu-id="7e033-197">Visual Studio が Azure にアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7e033-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="7e033-198">デプロイが完了すると、ブラウザーでアプリが開きます。</span><span class="sxs-lookup"><span data-stu-id="7e033-198">When the deployment completes, the app is opened in a browser.</span></span>

![[発行] ダイアログ:[設定] パネル](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="7e033-200">アプリを更新する</span><span class="sxs-lookup"><span data-stu-id="7e033-200">Update the app</span></span>

* <span data-ttu-id="7e033-201">*Pages/Index.cshtml* Razor ページを編集し、その内容を変更します。</span><span class="sxs-lookup"><span data-stu-id="7e033-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="7e033-202">たとえば、"Hello ASP.NET Core!" と表示されるように段落を修正できます。</span><span class="sxs-lookup"><span data-stu-id="7e033-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="7e033-203">**発行プロファイルの概要** ページから **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![発行プロファイルの概要ページ](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="7e033-205">アプリが発行されたら、Azure に変更内容が反映されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7e033-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![タスクの完了を確認します。](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="7e033-207">クリーンアップ</span><span class="sxs-lookup"><span data-stu-id="7e033-207">Clean up</span></span>

<span data-ttu-id="7e033-208">アプリのテストが完了したら、[Azure Portal](https://portal.azure.com/) に移動し、アプリを削除します。</span><span class="sxs-lookup"><span data-stu-id="7e033-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="7e033-209">**[リソース グループ]** を選択し、作成したリソース グループを選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Azure Portal: サイドバー メニューの [リソース グループ]](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="7e033-211">**[リソース グループ]** ページで、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal:[リソース グループ] ページ](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="7e033-213">リソース グループ名を入力し、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7e033-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="7e033-214">このチュートリアルで作成されたアプリとその他すべてのリソースが Azure から削除されます。</span><span class="sxs-lookup"><span data-stu-id="7e033-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="7e033-215">次の手順</span><span class="sxs-lookup"><span data-stu-id="7e033-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="7e033-216">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7e033-216">Additional resources</span></span>

* <span data-ttu-id="7e033-217">Visual Studio Code については、「[発行プロファイル](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7e033-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="7e033-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7e033-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="7e033-219">Azure リソース グループ</span><span class="sxs-lookup"><span data-stu-id="7e033-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="7e033-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="7e033-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>