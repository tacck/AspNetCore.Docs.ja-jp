---
title: SignalRAzure App Service に ASP.NET Core アプリを発行する
author: bradygaster
description: ASP.NET Core アプリを Azure App Service に発行する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d3f48b3171012b03fcaf7665c2089b27d37bbeca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408839"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="29b48-103">SignalRAzure App Service に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="29b48-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="29b48-104">[Brady](https://twitter.com/bradygaster)による</span><span class="sxs-lookup"><span data-stu-id="29b48-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="29b48-105">[Azure App Service](/azure/app-service/app-service-web-overview)は、web アプリ (ASP.NET Core を含む) をホストするための[Microsoft クラウドコンピューティング](https://azure.microsoft.com/)プラットフォームサービスです。</span><span class="sxs-lookup"><span data-stu-id="29b48-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="29b48-106">この記事では、Visual Studio から ASP.NET Core アプリを発行する方法について説明 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="29b48-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="29b48-107">詳細については、「 [ SignalR Azure のサービス](https://azure.microsoft.com/services/signalr-service)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29b48-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="29b48-108">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="29b48-108">Publish the app</span></span>

<span data-ttu-id="29b48-109">この記事では、Visual Studio のツールを使用した発行について説明します。</span><span class="sxs-lookup"><span data-stu-id="29b48-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="29b48-110">Visual Studio Code ユーザーは、 [Azure CLI](/cli/azure)のコマンドを使用して Azure にアプリを発行できます。</span><span class="sxs-lookup"><span data-stu-id="29b48-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="29b48-111">詳細については、「[コマンドラインツールを使用した Azure への ASP.NET Core アプリの発行](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29b48-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="29b48-112">**[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b48-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="29b48-113">[**発行先の選択**] ダイアログで [ **App Service**と**新規作成**] が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="29b48-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="29b48-114">[**発行**] ボタンドロップダウンから [**プロファイルの作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b48-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="29b48-115">[ **App Service の作成**] ダイアログで、次の表に記載されている情報を入力し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b48-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="29b48-116">項目</span><span class="sxs-lookup"><span data-stu-id="29b48-116">Item</span></span>               | <span data-ttu-id="29b48-117">説明</span><span class="sxs-lookup"><span data-stu-id="29b48-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="29b48-118">**名前**</span><span class="sxs-lookup"><span data-stu-id="29b48-118">**Name**</span></span>           | <span data-ttu-id="29b48-119">アプリの一意の名前。</span><span class="sxs-lookup"><span data-stu-id="29b48-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="29b48-120">**サブスクリプション**</span><span class="sxs-lookup"><span data-stu-id="29b48-120">**Subscription**</span></span>   | <span data-ttu-id="29b48-121">アプリが使用する Azure サブスクリプション。</span><span class="sxs-lookup"><span data-stu-id="29b48-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="29b48-122">**リソース グループ**</span><span class="sxs-lookup"><span data-stu-id="29b48-122">**Resource Group**</span></span> | <span data-ttu-id="29b48-123">アプリが所属する関連リソースのグループ。</span><span class="sxs-lookup"><span data-stu-id="29b48-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="29b48-124">**ホスティング プラン**</span><span class="sxs-lookup"><span data-stu-id="29b48-124">**Hosting Plan**</span></span>   | <span data-ttu-id="29b48-125">Web アプリの料金プラン。</span><span class="sxs-lookup"><span data-stu-id="29b48-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="29b48-126">[**依存関係**の追加] ドロップダウンリストで**Azure SignalR サービス**を選択し  >  **Add**ます。</span><span class="sxs-lookup"><span data-stu-id="29b48-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   <span data-ttu-id="29b48-127">![SignalR[追加] ドロップダウンリストでの Azure サービスの選択を示す [依存関係] 領域](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span><span class="sxs-lookup"><span data-stu-id="29b48-127">![Dependencies area showing the selection of Azure SignalR Service in the Add drop-down list](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span></span>

1. <span data-ttu-id="29b48-128">[ **Azure SignalR サービス**] ダイアログで、[**新しい Azure SignalR サービスインスタンスの作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b48-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="29b48-129">**名前**、**リソースグループ**、および**場所**を指定します。</span><span class="sxs-lookup"><span data-stu-id="29b48-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="29b48-130">[ **Azure SignalR サービス**] ダイアログに戻り、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b48-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="29b48-131">Visual Studio は次のタスクを完了します。</span><span class="sxs-lookup"><span data-stu-id="29b48-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="29b48-132">発行設定を含む発行プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="29b48-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="29b48-133">指定された詳細を使用して*Azure Web アプリ*を作成します。</span><span class="sxs-lookup"><span data-stu-id="29b48-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="29b48-134">アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="29b48-134">Publishes the app.</span></span>
* <span data-ttu-id="29b48-135">ブラウザーを起動して、web アプリを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="29b48-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="29b48-136">アプリの URL の形式は `{APP SERVICE NAME}.azurewebsites.net` です。</span><span class="sxs-lookup"><span data-stu-id="29b48-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="29b48-137">たとえば、という名前 `SignalRChatApp` のアプリの URL は `https://signalrchatapp.azurewebsites.net` です。</span><span class="sxs-lookup"><span data-stu-id="29b48-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="29b48-138">Preview .NET Core リリースを対象とするアプリをデプロイするときに HTTP *502.2-Bad Gateway*エラーが発生する場合は、「 [ASP.NET Core プレビューリリース Azure App Service をデプロイ](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)して解決するには」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29b48-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="29b48-139">Azure App Service でアプリを構成する</span><span class="sxs-lookup"><span data-stu-id="29b48-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="29b48-140">*このセクションは、Azure サービスを使用していないアプリにのみ適用さ SignalR れます。*</span><span class="sxs-lookup"><span data-stu-id="29b48-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="29b48-141">アプリで Azure サービスを使用する場合、App Service では、 SignalR このセクションで説明するアプリケーション要求ルーティング (ARR) アフィニティと Web ソケットの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="29b48-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="29b48-142">クライアントは、 SignalR アプリに直接ではなく、Azure サービスに Web ソケットを接続します。</span><span class="sxs-lookup"><span data-stu-id="29b48-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="29b48-143">Azure サービスを使用せずにホストされるアプリの場合は SignalR 、次を有効にします。</span><span class="sxs-lookup"><span data-stu-id="29b48-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="29b48-144">[ARR アフィニティ](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)は、ユーザーから同じ App Service インスタンスに要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="29b48-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="29b48-145">既定の設定は**On**です。</span><span class="sxs-lookup"><span data-stu-id="29b48-145">The default setting is **On**.</span></span>
* <span data-ttu-id="29b48-146">Web ソケットトランスポートを機能させるための[Web ソケット](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="29b48-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="29b48-147">既定の設定は [**オフ**] です。</span><span class="sxs-lookup"><span data-stu-id="29b48-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="29b48-148">Azure portal で、 **App Services**内の web アプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="29b48-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="29b48-149">**構成**  >  の**全般設定**を開きます。</span><span class="sxs-lookup"><span data-stu-id="29b48-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="29b48-150">**Web ソケット**を**オン**に設定します。</span><span class="sxs-lookup"><span data-stu-id="29b48-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="29b48-151">**ARR affinity**が**On**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="29b48-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="29b48-152">App Service プランの制限</span><span class="sxs-lookup"><span data-stu-id="29b48-152">App Service Plan limits</span></span>

<span data-ttu-id="29b48-153">Web ソケットとその他のトランスポートは、選択した App Service プランに基づいて制限されます。</span><span class="sxs-lookup"><span data-stu-id="29b48-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="29b48-154">詳細については、azure[サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits#app-service-limits)に関する記事の*azure Cloud Services の制限*と*App Service の制限事項*に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="29b48-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29b48-155">その他の資料</span><span class="sxs-lookup"><span data-stu-id="29b48-155">Additional resources</span></span>

* <span data-ttu-id="29b48-156">[Azure サービスとは SignalR](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="29b48-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="29b48-157">コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを公開する</span><span class="sxs-lookup"><span data-stu-id="29b48-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="29b48-158">Azure で ASP.NET Core プレビューアプリをホストしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="29b48-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
