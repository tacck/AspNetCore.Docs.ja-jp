---
title: SignalRAzure App Service に ASP.NET Core アプリを発行する
author: bradygaster
description: ASP.NET Core アプリを Azure App Service に発行する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 3163a2743c544d0a07d92eb7547e77c3b200ac83
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022538"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>SignalRAzure App Service に ASP.NET Core アプリを発行する

[Brady](https://twitter.com/bradygaster)による

[Azure App Service](/azure/app-service/app-service-web-overview)は、web アプリ (ASP.NET Core を含む) をホストするための[Microsoft クラウドコンピューティング](https://azure.microsoft.com/)プラットフォームサービスです。

> [!NOTE]
> この記事では、Visual Studio から ASP.NET Core アプリを発行する方法について説明 SignalR します。 詳細については、「 [ SignalR Azure のサービス](https://azure.microsoft.com/services/signalr-service)」を参照してください。

## <a name="publish-the-app"></a>アプリの発行

この記事では、Visual Studio のツールを使用した発行について説明します。 Visual Studio Code ユーザーは、 [Azure CLI](/cli/azure)のコマンドを使用して Azure にアプリを発行できます。 詳細については、「[コマンドラインツールを使用した Azure への ASP.NET Core アプリの発行](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。

1. [**発行先の選択**] ダイアログで [ **App Service**と**新規作成**] が選択されていることを確認します。

1. [**発行**] ボタンドロップダウンから [**プロファイルの作成**] を選択します。

   [ **App Service の作成**] ダイアログで、次の表に記載されている情報を入力し、[**作成**] を選択します。

   | Item               | 説明 |
   | ------------------ | ----------- |
   | **Name**           | アプリの一意の名前。 |
   | **[サブスクリプション]**   | アプリが使用する Azure サブスクリプション。 |
   | **[リソース グループ]** | アプリが所属する関連リソースのグループ。 |
   | **ホスティング プラン**   | Web アプリの料金プラン。 |

1. [**依存関係**の追加] ドロップダウンリストで**Azure SignalR サービス**を選択し  >  **Add**ます。

   ![[追加] ドロップダウンリストに表示されている Azure::: no-loc (SignalR)::: Service の選択を示す [依存関係] 領域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. [ **Azure SignalR サービス**] ダイアログで、[**新しい Azure SignalR サービスインスタンスの作成**] を選択します。

1. **名前**、**リソースグループ**、および**場所**を指定します。 [ **Azure SignalR サービス**] ダイアログに戻り、[**追加**] を選択します。

Visual Studio は次のタスクを完了します。

* 発行設定を含む発行プロファイルを作成します。
* 指定された詳細を使用して*Azure Web アプリ*を作成します。
* アプリを発行します。
* ブラウザーを起動して、web アプリを読み込みます。

アプリの URL の形式は `{APP SERVICE NAME}.azurewebsites.net` です。 たとえば、という名前 `SignalRChatApp` のアプリの URL は `https://signalrchatapp.azurewebsites.net` です。

Preview .NET Core リリースを対象とするアプリをデプロイするときに HTTP *502.2-Bad Gateway*エラーが発生する場合は、「 [ASP.NET Core プレビューリリース Azure App Service をデプロイ](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)して解決するには」を参照してください。

## <a name="configure-the-app-in-azure-app-service"></a>Azure App Service でアプリを構成する

> [!NOTE]
> *このセクションは、Azure サービスを使用していないアプリにのみ適用さ SignalR れます。*
>
> アプリで Azure サービスを使用する場合、App Service では、 SignalR このセクションで説明するアプリケーション要求ルーティング (ARR) アフィニティと Web ソケットの構成は必要ありません。 クライアントは、 SignalR アプリに直接ではなく、Azure サービスに Web ソケットを接続します。

Azure サービスを使用せずにホストされるアプリの場合は SignalR 、次を有効にします。

* [ARR Affinity]( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(ARR- cookie ) -for-Azure-web-apps.html) ユーザーから同じ App Service インスタンスに要求をルーティングします。 既定の設定は**On**です。
* Web ソケットトランスポートを機能させるための[Web ソケット](xref:fundamentals/websockets)。 既定の設定は [**オフ**] です。

1. Azure portal で、 **App Services**内の web アプリに移動します。
1. **構成**  >  の**全般設定**を開きます。
1. **Web ソケット**を**オン**に設定します。
1. **ARR affinity**が**On**に設定されていることを確認します。

## <a name="app-service-plan-limits"></a>App Service プランの制限

Web ソケットとその他のトランスポートは、選択した App Service プランに基づいて制限されます。 詳細については、azure[サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits#app-service-limits)に関する記事の*azure Cloud Services の制限*と*App Service の制限事項*に関するセクションを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [Azure サービスとは SignalR](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを公開する](/azure/app-service/app-service-web-get-started-dotnet)
* [Azure で ASP.NET Core プレビューアプリをホストしてデプロイする](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
