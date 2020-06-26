---
title: Docker コンテナーで ASP.NET Core をホストする
author: rick-anderson
description: Docker コンテナーで ASP.NET Core アプリをホストする方法についてのリソースへのリンクを検出します。
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/index
ms.openlocfilehash: 14758c0d35841a077b36578786402c80fa3c3b1d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408059"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Docker コンテナーで ASP.NET Core をホストする

以下の記事では、Docker での ASP.NET Core アプリのホスティングについて説明されています。

[コンテナーと Docker の概要](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
コンテナリゼーションは、ソフトウェア開発のアプローチであり、アプリケーションまたはサービス、その依存関係、その構成がコンテナー イメージとしてどのようにパッケージ化されるかを示します。 イメージは、テストしてからホストに展開することができます。

[Docker について](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
オープン ソース プロジェクトの Docker が、クラウドまたはオンプレミスで実行できる移植可能な自己完結型のコンテナーとしてアプリの展開を自動化するしくみを説明します。

[Docker に関する用語](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Docker 技術の用語と定義について学習します。

[Docker のコンテナー、イメージ、およびレジストリ](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
環境全体での一貫性のある展開のため、Docker コンテナー イメージがイメージ レジストリに保存されるしくみを確認します。

<xref:host-and-deploy/docker/building-net-docker-images> ASP.NET Core アプリをビルドし、Docker で動作させる方法について学習します。 Microsoft によって管理される Docker イメージを確認し、ユース ケースを調査します。

[Visual Studio コンテナー ツール](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Docker for Windows 上での .NET Framework または .NET Core のいずれかをターゲットとする ASP.NET Core アプリのビルド、デバッグ、および実行が Visual Studio によってどのようにサポートされるかについて説明します。 Windows と Linux の両方のコンテナーがサポートされます。

[Azure Container Registry に発行する](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Visual Studio Container Tools 拡張機能を使用して、PowerShell を使用する Azure で Docker ホストに ASP.NET Core アプリを展開する方法を確認します。

[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)  
プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。 プロキシ経由で要求を渡すと、スキームやクライアントの IP アドレスなど、元の要求に関する情報が不明になることがよくあります。 その場合、要求に関する情報をアプリに手動で転送しなければならないことがあります。

[Docker と小さなコンテナーを使用しての GC](xref:performance/memory#sc) 小さなコンテナーを使用しての GC の選択について。