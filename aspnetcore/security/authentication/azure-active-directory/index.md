---
title: ASP.NET Core を使用した Microsoft ID プラットフォームと Azure Active Directory
author: rick-anderson
description: ASP.NET Core における Web アプリと API のための Microsoft ID プラットフォームと Azure Active Directory での認証に関連するトピックを紹介します。
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061406"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="7aa94-103">Azure Active Directory と ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7aa94-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="7aa94-104">これらのチュートリアルとサンプルは、Microsoft ID プラットフォームと Azure Active Directory を使用した ASP.NET Core での認証を示します。</span><span class="sxs-lookup"><span data-stu-id="7aa94-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="7aa94-105">Azure AD での ASP.NET Core を使用したその他のチュートリアルとサンプルについては、「[Microsoft ID プラットフォーム](/azure/active-directory/develop/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7aa94-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="7aa94-106">アプリケーション シナリオ</span><span class="sxs-lookup"><span data-stu-id="7aa94-106">Application Scenarios</span></span>

* [<span data-ttu-id="7aa94-107">クイック スタート:Microsoft でのサインインを ASP.NET Core Web アプリに追加する</span><span class="sxs-lookup"><span data-stu-id="7aa94-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="7aa94-108">ユーザーがサインインする Web アプリ</span><span class="sxs-lookup"><span data-stu-id="7aa94-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="7aa94-109">Web API を呼び出す Web アプリ</span><span class="sxs-lookup"><span data-stu-id="7aa94-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="7aa94-110">保護された Web API</span><span class="sxs-lookup"><span data-stu-id="7aa94-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="7aa94-111">他の Web API を呼び出す Web アプリ</span><span class="sxs-lookup"><span data-stu-id="7aa94-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="7aa94-112">Azure AD B2C にユーザーをサインインさせる Web アプリ</span><span class="sxs-lookup"><span data-stu-id="7aa94-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="7aa94-113">サンプル</span><span class="sxs-lookup"><span data-stu-id="7aa94-113">Samples</span></span>

* <span data-ttu-id="7aa94-114">[Azure AD V2 を使用して ASP.NET Core アプリでユーザーをサインインさせ、Web API が呼び出せるようにする](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="7aa94-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="7aa94-115">[こちらの関連ビデオ](https://channel9.msdn.com/Events/Build/2018/THR5001)をご覧ください</span><span class="sxs-lookup"><span data-stu-id="7aa94-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="7aa94-116">[Azure AD V2 を使用した WPF アプリケーションからの ASP.NET Core 2.0 Web API の呼び出し](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="7aa94-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="7aa94-117">[こちらの関連ビデオ](https://channel9.msdn.com/Events/Build/2018/THR5000)をご覧ください</span><span class="sxs-lookup"><span data-stu-id="7aa94-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="7aa94-118">ASP.NET Core Web アプリでの Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="7aa94-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
