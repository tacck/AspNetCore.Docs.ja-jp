---
title: ASP.NET Core での一般データ保護規則 (GDPR) のサポート
author: rick-anderson
description: ASP.NET Core web アプリの GDPR 拡張ポイントにアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 8a7041a976ea9f0e99bfd1eba792d0e919eaf6d3
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212823"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9528b-103">ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート</span><span class="sxs-lookup"><span data-stu-id="9528b-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9528b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9528b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9528b-105">ASP.NET Core は、一部の[EU 一般データ保護規則 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en)要件を満たすための api とテンプレートを提供します。</span><span class="sxs-lookup"><span data-stu-id="9528b-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9528b-106">プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="9528b-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9528b-107">*Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9528b-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9528b-108">ASP.NET Core 3.0 テンプレートで生成されたアプリの ASP.NET Core 2.2 テンプレートにあるような既定の cookie 同意機能を有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9528b-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9528b-109">`using Microsoft.AspNetCore.Http`Using ディレクティブのリストにを追加します。</span><span class="sxs-lookup"><span data-stu-id="9528b-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9528b-110">[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)を `Startup.ConfigureServices` と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)に追加し `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9528b-111">Cookie 同意部分を *_Layout*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9528b-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9528b-112">\* \_ CookieConsentPartial\*ファイルをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="9528b-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="9528b-113">Cookie の同意機能については、この記事の ASP.NET Core 2.2 バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="9528b-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9528b-114">プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、プライバシーと cookie の使用ポリシーに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="9528b-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9528b-115">Cookie の同意機能を使用すると、個人情報の保存をユーザーに要求する (および追跡する) ことができます。</span><span class="sxs-lookup"><span data-stu-id="9528b-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9528b-116">ユーザーがデータ収集に同意しておらず、アプリの[Checkcon](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)がに設定されている場合 `true` 、必須ではない cookie はブラウザーに送信されません。</span><span class="sxs-lookup"><span data-stu-id="9528b-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="9528b-117">Cookie は、必須としてマークできます。</span><span class="sxs-lookup"><span data-stu-id="9528b-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="9528b-118">ユーザーが同意しておらず、追跡が無効になっている場合でも、必須の cookie がブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9528b-119">追跡が無効になっている場合[、Tempdata とセッションクッキー](#tempdata)は機能しません。</span><span class="sxs-lookup"><span data-stu-id="9528b-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9528b-120">[ [ Identity 管理](#pd)] ページには、ユーザーデータをダウンロードおよび削除するためのリンクが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9528b-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9528b-121">この[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を使用すると、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと api の大部分をテストできます。</span><span class="sxs-lookup"><span data-stu-id="9528b-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9528b-122">テスト手順については、 [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9528b-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9528b-123">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9528b-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9528b-124">テンプレートで生成されたコードでの GDPR サポートの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9528b-124">ASP.NET Core GDPR support in template-generated code</span></span>

Razor<span data-ttu-id="9528b-125">プロジェクトテンプレートで作成されたページと MVC プロジェクトには、次の GDPR サポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9528b-125"> Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9528b-126">[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)はクラスで設定され `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9528b-127">\* \_ CookieConsentPartial\* [部分ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="9528b-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9528b-128">このファイルには、[ **Accept** ] ボタンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9528b-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9528b-129">ユーザーが [**同意**する] ボタンをクリックすると、[cookie の保存に同意する] が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="9528b-130">*Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9528b-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9528b-131">\* \_ CookieConsentPartial\*ファイルは、プライバシーページへのリンクを生成します。</span><span class="sxs-lookup"><span data-stu-id="9528b-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9528b-132">個々のユーザーアカウントで作成されたアプリの場合、[管理] ページに、[個人ユーザーデータ](#pd)をダウンロードおよび削除するためのリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="9528b-133">CookiePolicyOptions および UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="9528b-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="9528b-134">[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)は次のもので初期化され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9528b-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は次のもので呼び出され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9528b-136">\_CookieConsentPartial 部分ビュー</span><span class="sxs-lookup"><span data-stu-id="9528b-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9528b-137">\* \_ CookieConsentPartial\*部分ビューは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9528b-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="9528b-138">この部分は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9528b-138">This partial:</span></span>

* <span data-ttu-id="9528b-139">ユーザーの追跡の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="9528b-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9528b-140">アプリが同意を要求するように構成されている場合、ユーザーは、cookie を追跡する前に同意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9528b-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="9528b-141">同意が必要な場合、cookie 同意パネルは、 \* \_ Layout\*ファイルによって作成されたナビゲーションバーの上部に固定されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9528b-142">`<p>`プライバシーと cookie の使用ポリシーを要約する HTML 要素を提供します。</span><span class="sxs-lookup"><span data-stu-id="9528b-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9528b-143">サイトのプライバシーポリシーを詳細に説明する [プライバシー] ページまたはビューへのリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="9528b-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="9528b-144">必須の cookie</span><span class="sxs-lookup"><span data-stu-id="9528b-144">Essential cookies</span></span>

<span data-ttu-id="9528b-145">Cookie を保存するための同意が指定されていない場合は、必須とマークされた cookie だけがブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="9528b-146">次のコードでは、cookie が必要になります。</span><span class="sxs-lookup"><span data-stu-id="9528b-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="9528b-147">TempData プロバイダーとセッション状態の cookie は必須ではありません</span><span class="sxs-lookup"><span data-stu-id="9528b-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="9528b-148">[Tempdata プロバイダー](xref:fundamentals/app-state#tempdata)の cookie は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="9528b-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="9528b-149">追跡が無効になっている場合、TempData プロバイダーは機能しません。</span><span class="sxs-lookup"><span data-stu-id="9528b-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9528b-150">追跡が無効になっているときに TempData プロバイダーを有効にするには、TempData cookie をに必須としてマークし `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9528b-151">[セッション状態](xref:fundamentals/app-state)の cookie は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="9528b-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="9528b-152">追跡が無効になっている場合、セッション状態は機能しません。</span><span class="sxs-lookup"><span data-stu-id="9528b-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9528b-153">次のコードでは、セッション cookie が不可欠になります。</span><span class="sxs-lookup"><span data-stu-id="9528b-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9528b-154">個人データ</span><span class="sxs-lookup"><span data-stu-id="9528b-154">Personal data</span></span>

<span data-ttu-id="9528b-155">個々のユーザーアカウントで作成されたアプリには、個人データをダウンロードして削除するコードが含まれています。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9528b-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9528b-156">ユーザー名を選択し、[**個人データ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9528b-156">Select the user name and then select **Personal data**:</span></span>

![[個人データの管理] ページ](gdpr/_static/pd.png)

<span data-ttu-id="9528b-158">注:</span><span class="sxs-lookup"><span data-stu-id="9528b-158">Notes:</span></span>

* <span data-ttu-id="9528b-159">コードを生成するには `Account/Manage` 、「[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9528b-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9528b-160">[**削除**] リンクと [**ダウンロード**] リンクは、既定の id データに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="9528b-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9528b-161">カスタムユーザーデータを作成するアプリは、カスタムユーザーデータを削除/ダウンロードするように拡張する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9528b-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9528b-162">詳細については、「[カスタムユーザーデータの追加、ダウンロード、 Identity および削除」を](xref:security/authentication/add-user-data)参照してください。</span><span class="sxs-lookup"><span data-stu-id="9528b-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9528b-163">データベーステーブルに格納されているユーザーの保存済みトークン Identity `AspNetUserTokens` は、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)による連鎖削除動作によってユーザーが削除されると削除されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="9528b-164">Cookie ポリシーを受け入れる前に、Facebook や Google などの[外部プロバイダー認証](xref:security/authentication/social/index)は使用できません。</span><span class="sxs-lookup"><span data-stu-id="9528b-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9528b-165">保存時の暗号化</span><span class="sxs-lookup"><span data-stu-id="9528b-165">Encryption at rest</span></span>

<span data-ttu-id="9528b-166">一部のデータベースとストレージメカニズムでは、保存時の暗号化が可能です。</span><span class="sxs-lookup"><span data-stu-id="9528b-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9528b-167">保存時の暗号化:</span><span class="sxs-lookup"><span data-stu-id="9528b-167">Encryption at rest:</span></span>

* <span data-ttu-id="9528b-168">保存されたデータを自動的に暗号化します。</span><span class="sxs-lookup"><span data-stu-id="9528b-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9528b-169">は、データにアクセスするソフトウェアの構成、プログラミング、またはその他の作業を行わずに暗号化します。</span><span class="sxs-lookup"><span data-stu-id="9528b-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9528b-170">は最も簡単で安全なオプションです。</span><span class="sxs-lookup"><span data-stu-id="9528b-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9528b-171">データベースでキーと暗号化を管理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="9528b-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9528b-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9528b-172">For example:</span></span>

* <span data-ttu-id="9528b-173">Microsoft SQL と Azure SQL は、 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) を提供します。</span><span class="sxs-lookup"><span data-stu-id="9528b-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9528b-174">既定では、SQL Azure によってデータベースが暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="9528b-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9528b-175">[既定では、Azure の blob、ファイル、テーブル、および Queue Storage が暗号化され](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)ます。</span><span class="sxs-lookup"><span data-stu-id="9528b-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9528b-176">保存時の組み込みの暗号化を提供しないデータベースの場合は、ディスク暗号化を使用して同じ保護を提供できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9528b-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9528b-177">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9528b-177">For example:</span></span>

* [<span data-ttu-id="9528b-178">Windows Server 用の BitLocker</span><span class="sxs-lookup"><span data-stu-id="9528b-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9528b-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="9528b-179">Linux:</span></span>
  * [<span data-ttu-id="9528b-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="9528b-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9528b-181">[Encfs](https://github.com/vgough/encfs)。</span><span class="sxs-lookup"><span data-stu-id="9528b-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9528b-182">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9528b-182">Additional resources</span></span>

* [<span data-ttu-id="9528b-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9528b-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="9528b-184">GDPR-ASP.NET Core に Revoke 同意ボタンを追加する</span><span class="sxs-lookup"><span data-stu-id="9528b-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
