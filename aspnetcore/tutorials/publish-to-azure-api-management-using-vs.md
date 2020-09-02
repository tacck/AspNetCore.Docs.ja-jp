---
title: Visual Studio を使用して Azure API Management に ASP.NET Core Web API を発行する
author: codemillmatt
description: Visual Studio を使用して Azure API Management に ASP.NET Core Web API を発行する方法を説明します。
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945484"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="cc04c-103">Visual Studio を使用して Azure API Management に ASP.NET Core Web API を発行する</span><span class="sxs-lookup"><span data-stu-id="cc04c-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="cc04c-104">作成者: [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="cc04c-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="cc04c-105">設定</span><span class="sxs-lookup"><span data-stu-id="cc04c-105">Set up</span></span>

- <span data-ttu-id="cc04c-106">Azure アカウントをお持ちでない場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="cc04c-107">まだ行っていない場合は、[新しい Azure API Management インスタンスを作成します](/azure/api-management/get-started-create-service-instance)。</span><span class="sxs-lookup"><span data-stu-id="cc04c-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="cc04c-108">[Web API アプリ プロジェクトを作成します](xref:tutorials/first-web-api#create-a-web-project)。</span><span class="sxs-lookup"><span data-stu-id="cc04c-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="cc04c-109">Configure the app</span><span class="sxs-lookup"><span data-stu-id="cc04c-109">Configure the app</span></span>

<span data-ttu-id="cc04c-110">Swagger 定義を ASP.NET Core Web API に追加すると、Azure API Management でアプリの API 定義を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="cc04c-111">次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="cc04c-112">Swagger を追加する</span><span class="sxs-lookup"><span data-stu-id="cc04c-112">Add Swagger</span></span>

1. <span data-ttu-id="cc04c-113">[Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet パッケージを ASP.NET Core Web API プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![NuGet ダイアログの構成のスクリーンショット](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="cc04c-115">次の行を `Startup.ConfigureServices` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="cc04c-116">次の行を `Startup.Configure` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="cc04c-117">API ルーティングを変更する</span><span class="sxs-lookup"><span data-stu-id="cc04c-117">Change the API routing</span></span>

<span data-ttu-id="cc04c-118">次に、`WeatherForecastController`の `Get` アクションにアクセスするために必要な URL 構造を変更します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="cc04c-119">次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-119">Complete the following steps:</span></span>

1. <span data-ttu-id="cc04c-120">*WeatherForecastController.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="cc04c-121">クラス レベルの `[Route("[controller]")]` 属性を削除します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="cc04c-122">クラスの定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="cc04c-123">`Get()` アクションに `[Route("/")]` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="cc04c-124">関数の定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="cc04c-125">Azure App Service に Web API を発行する</span><span class="sxs-lookup"><span data-stu-id="cc04c-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="cc04c-126">ASP.NET Core Web API を Azure API Management に発行するには、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="cc04c-127">API アプリを Azure App Service に発行します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="cc04c-128">Azure API Management サービス インスタンスに空の API を追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="cc04c-129">ASP.NET Core Web API アプリを Azure API Management サービス インスタンスに発行します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="cc04c-130">API アプリを Azure App Service に発行する</span><span class="sxs-lookup"><span data-stu-id="cc04c-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="cc04c-131">ASP.NET Core Web API を Azure API Management に発行するには、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="cc04c-132">**ソリューション エクスプローラー**で、プロジェクトを右クリックして、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="cc04c-134">**[発行]** ダイアログで **[Azure]** を選択し、 **[次へ]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![[発行] ダイアログ](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="cc04c-136">**[Azure App Service (Windows)]** を選択し、 **[次へ]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![[発行] ダイアログ: App Service を選択する](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="cc04c-138">**[新しい Azure App Service の作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-138">Select **Create a new Azure App Service**.</span></span>

    ![[発行] ダイアログ: Azure サービス インスタンスを選択](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="cc04c-140">**[App Service の作成]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="cc04c-141">**[アプリ名]** 、 **[リソース グループ]** 、 **[App Service プラン]** の各入力フィールドに値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="cc04c-142">これらの名前を保持することも、変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="cc04c-143">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-143">Select the **Create** button.</span></span>

    ![[App Service の作成] ダイアログ](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="cc04c-145">作成が完了すると、ダイアログが自動的に閉じられ、 **[発行]** ダイアログに再度フォーカスが移ります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="cc04c-146">作成されたインスタンスが自動的に選択されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-146">The instance that was created is automatically selected.</span></span>

![[発行] ダイアログ: App Service インスタンスを選択](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="cc04c-148">この時点で、Azure API Management サービスに API を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="cc04c-149">次のタスクを行っている間は、Visual Studio を開いたままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="cc04c-150">Azure API Management に API を追加する</span><span class="sxs-lookup"><span data-stu-id="cc04c-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="cc04c-151">前に作成した API Management サービス インスタンスを Azure portal で開きます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="cc04c-152">**[API]** ブレードを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-152">Select the **APIs** blade:</span></span>

    ![API Management サービス インスタンスから選択した [API] ブレード](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="cc04c-154">**[Add a new API]\(新しい API の追加\)** パネルで、 **[空の API]** タイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![[空の API] タイルが強調表示されている画面](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="cc04c-156">表示される **[空の API の作成]** ダイアログで、次の値を入力します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="cc04c-157">**表示名**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="cc04c-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="cc04c-158">**名前**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="cc04c-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="cc04c-159">**API URL サフィックス**: *v1*</span><span class="sxs-lookup"><span data-stu-id="cc04c-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="cc04c-160">**[Web サービス URL]** フィールドは空のままにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="cc04c-161">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-161">Select the **Create** button.</span></span>

    ![完成した [空の API の作成] ダイアログのスクリーンショット](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="cc04c-163">空の API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-163">The blank API is created.</span></span>

![API Management ポータルでの空の API のスクリーンショット](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="cc04c-165">Azure API Management に ASP.NET Core Web API を発行する</span><span class="sxs-lookup"><span data-stu-id="cc04c-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="cc04c-166">Visual Studio に戻ります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="cc04c-167">前にそのままにしておいた **[発行]** ダイアログ ボックスがまだ開いているはずです。</span><span class="sxs-lookup"><span data-stu-id="cc04c-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="cc04c-168">新しく発行した Azure App Service を選択して強調表示にします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="cc04c-169">**[次へ]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-169">Select the **Next** button.</span></span>

    ![App Service が選択されている [発行] ダイアログのスクリーンショット](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="cc04c-171">ダイアログに、前に作成した Azure API Management サービスが表示されるようになります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="cc04c-172">それと *[APIs]* フォルダーを展開して、作成された空の API を表示します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="cc04c-173">空の API の名前を選択して、 **[完了]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![[発行] ダイアログで選択された、新しく作成された Azure API Management の空の API のスクリーンショット](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="cc04c-175">ダイアログが閉じ、発行に関する情報が含まれる概要画面が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="cc04c-176">**[発行]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-176">Select the **Publish** button.</span></span>

    ![発行プロファイルが表示されている Visual Studio のスクリーンショット](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="cc04c-178">Web API は、Azure App Service と Azure API Management の両方に発行されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="cc04c-179">新しいブラウザー ウィンドウが表示され、Azure App Service で実行されている API が示されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="cc04c-180">そのウィンドウは閉じてもかまいません。</span><span class="sxs-lookup"><span data-stu-id="cc04c-180">You can close that window.</span></span>

1. <span data-ttu-id="cc04c-181">Azure portal で Azure API Management インスタンスに戻ります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="cc04c-182">ブラウザー ウィンドウを更新します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="cc04c-183">前の手順で作成した空の API を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="cc04c-184">内容が設定されているので、調べることができます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-184">It's now populated and you can explore around.</span></span>

    ![Azure API Management で設定された API のスクリーンショット](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="cc04c-186">発行された API の名前を構成する</span><span class="sxs-lookup"><span data-stu-id="cc04c-186">Configure the published API name</span></span>

<span data-ttu-id="cc04c-187">API の名前が、自分で付けたものとは異なることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc04c-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="cc04c-188">発行された API は *WeatherAPI* という名前になっています。しかし、作成したときには *WeatherForecasts* という名前を設定しました。</span><span class="sxs-lookup"><span data-stu-id="cc04c-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="cc04c-189">名前を修正するには、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="cc04c-189">Complete the following steps to fix the name:</span></span>

![異なる名前が強調して示されている、設定された API のスクリーンショット](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="cc04c-191">`Startup.ConfigureServices` メソッドから次の行を削除します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="cc04c-192">`Startup.ConfigureServices` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="cc04c-193">新しく作成された発行プロファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-193">Open the newly created publish profile.</span></span> <span data-ttu-id="cc04c-194">**ソリューション エクスプローラー**の *Properties/PublishProfiles* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![発行プロファイル ファイルの場所が強調して示されているスクリーンショット](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="cc04c-196">`<OpenAPIDocumentName>` 要素の値を `v1` から `WeatherForecasts` に変更します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![発行プロファイルに必要な変更のスクリーンショット](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="cc04c-198">ASP.NET Core Web API を再発行し、Azure portal で Azure API Management インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="cc04c-199">ブラウザーでページを更新します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-199">Refresh the page in your browser.</span></span> <span data-ttu-id="cc04c-200">API の名前が正しくなったことがわかります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-200">You'll see the name of the API is now correct.</span></span>

    ![完成した API のポータルでのスクリーンショット](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="cc04c-202">Web API が動作していることを確認する</span><span class="sxs-lookup"><span data-stu-id="cc04c-202">Verify the web API is working</span></span>

<span data-ttu-id="cc04c-203">次の手順のようにして、Azure API Management にデプロイされた ASP.NET Core Web API を Azure portal からテストできます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="cc04c-204">**[テスト]** タブを開きます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="cc04c-205">**[/]** または **[GET]** 操作を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="cc04c-206">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-206">Select **Send**.</span></span>

    ![テスト前のポータルのスクリーンショット](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="cc04c-208">成功した場合の応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="cc04c-208">A successful response will look like the following:</span></span>

![API Management からの正常な応答のスクリーンショット](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="cc04c-210">クリーンアップ</span><span class="sxs-lookup"><span data-stu-id="cc04c-210">Clean up</span></span>

<span data-ttu-id="cc04c-211">アプリのテストが完了したら、[Azure portal](https://portal.azure.com/) に移動して、アプリを削除します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="cc04c-212">**[リソース グループ]** を選択し、作成したリソース グループを選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure Portal: サイドバー メニューの [リソース グループ]](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="cc04c-214">**[リソース グループ]** ページで、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure Portal:[リソース グループ] ページ](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="cc04c-216">リソース グループ名を入力し、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cc04c-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="cc04c-217">このチュートリアルで作成されたアプリとその他すべてのリソースが Azure から削除されます。</span><span class="sxs-lookup"><span data-stu-id="cc04c-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cc04c-218">次の手順</span><span class="sxs-lookup"><span data-stu-id="cc04c-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="cc04c-219">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cc04c-219">Additional resources</span></span>

- [<span data-ttu-id="cc04c-220">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="cc04c-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="cc04c-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cc04c-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
