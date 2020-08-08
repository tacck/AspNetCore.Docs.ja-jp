---
title: ASP.NET Core での一般データ保護規則 (GDPR) のサポート
author: rick-anderson
description: ASP.NET Core web アプリの GDPR 拡張ポイントにアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: 6392a22e316f903da18cd1a91d1eb779d8dde1b3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020016"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="03057-103">ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート</span><span class="sxs-lookup"><span data-stu-id="03057-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="03057-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03057-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03057-105">ASP.NET Core は、一部の[EU 一般データ保護規則 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en)要件を満たすための api とテンプレートを提供します。</span><span class="sxs-lookup"><span data-stu-id="03057-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="03057-106">プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、お客様のプライバシーやポリシーを使用して置き換えることができ cookie ます。</span><span class="sxs-lookup"><span data-stu-id="03057-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="03057-107">*Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。</span><span class="sxs-lookup"><span data-stu-id="03057-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="03057-108">cookieASP.NET Core 3.0 テンプレートで生成されたアプリの ASP.NET Core 2.2 テンプレートにあるような既定の同意機能を有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="03057-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="03057-109">`using Microsoft.AspNetCore.Http`Using ディレクティブのリストにを追加します。</span><span class="sxs-lookup"><span data-stu-id="03057-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="03057-110">に[ Cookie policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)を追加 `Startup.ConfigureServices` し、 [ Cookie ポリシーを使用](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)して次のことを `Startup.Configure` 行います。</span><span class="sxs-lookup"><span data-stu-id="03057-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="03057-111">cookie同意部分を *_Layout*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="03057-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="03057-112">\* \_ Cookie Conの部分的\*なファイルをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="03057-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="03057-113">同意機能については、この記事の ASP.NET Core 2.2 バージョンを参照して cookie ください。</span><span class="sxs-lookup"><span data-stu-id="03057-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="03057-114">プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、お客様のプライバシーやポリシーを使用して置き換えることができ cookie ます。</span><span class="sxs-lookup"><span data-stu-id="03057-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="03057-115">同意機能を使用すると、 cookie 個人情報の保存をユーザーに要求 (および追跡) することができます。</span><span class="sxs-lookup"><span data-stu-id="03057-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="03057-116">ユーザーがデータ収集に同意しておらず、アプリの[Checkcon](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)がに設定されている場合 `true` 、非重要なは cookie ブラウザーに送信されません。</span><span class="sxs-lookup"><span data-stu-id="03057-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="03057-117">Cookieは、必須としてマークできます。</span><span class="sxs-lookup"><span data-stu-id="03057-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="03057-118">必須 cookie のは、ユーザーが同意しておらず、追跡が無効になっている場合でも、ブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="03057-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="03057-119">追跡が無効になっている場合[、Tempdata と Session cookie s](#tempdata)は機能しません。</span><span class="sxs-lookup"><span data-stu-id="03057-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="03057-120">[ [ Identity 管理](#pd)] ページには、ユーザーデータをダウンロードおよび削除するためのリンクが用意されています。</span><span class="sxs-lookup"><span data-stu-id="03057-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="03057-121">この[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を使用すると、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと api の大部分をテストできます。</span><span class="sxs-lookup"><span data-stu-id="03057-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="03057-122">テスト手順については、 [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03057-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="03057-123">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="03057-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="03057-124">テンプレートで生成されたコードでの GDPR サポートの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03057-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="03057-125">Razorプロジェクトテンプレートで作成されたページと MVC プロジェクトには、次の GDPR サポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="03057-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="03057-126">[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[Use Cookie policy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は、クラスで設定され `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="03057-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="03057-127">\* \_ Cookie Conの\*部分的な部分[ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="03057-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="03057-128">このファイルには、[ **Accept** ] ボタンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="03057-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="03057-129">ユーザーが [**同意**する] ボタンをクリックすると、store s に同意したことに cookie なります。</span><span class="sxs-lookup"><span data-stu-id="03057-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="03057-130">*Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。</span><span class="sxs-lookup"><span data-stu-id="03057-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="03057-131">\* \_ Cookie Con/partial\*ファイルは、プライバシーページへのリンクを生成します。</span><span class="sxs-lookup"><span data-stu-id="03057-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="03057-132">個々のユーザーアカウントで作成されたアプリの場合、[管理] ページに、[個人ユーザーデータ](#pd)をダウンロードおよび削除するためのリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="03057-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="03057-133">CookiePolicyOptions と Use Cookie policy</span><span class="sxs-lookup"><span data-stu-id="03057-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="03057-134">[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)は次のもので初期化され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="03057-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="03057-135">[使用 Cookieポリシー](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は次のもので呼び出され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="03057-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="03057-136">\_CookieConの部分的な部分ビューの表示</span><span class="sxs-lookup"><span data-stu-id="03057-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="03057-137">\* \_ Cookie Conの\*部分的な部分ビューは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="03057-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="03057-138">この部分は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="03057-138">This partial:</span></span>

* <span data-ttu-id="03057-139">ユーザーの追跡の状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="03057-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="03057-140">アプリが同意を要求するように構成されている場合、ユーザーはを追跡する前に同意する必要があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="03057-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="03057-141">同意が必要な場合は、 cookie \* \_ Layout\*ファイルによって作成されたナビゲーションバーの上部に同意パネルが固定されます。</span><span class="sxs-lookup"><span data-stu-id="03057-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="03057-142">`<p>`プライバシーを要約し、ポリシーを使用するための HTML 要素が用意されて cookie います。</span><span class="sxs-lookup"><span data-stu-id="03057-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="03057-143">サイトのプライバシーポリシーを詳細に説明する [プライバシー] ページまたはビューへのリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="03057-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="03057-144">必須 cookie</span><span class="sxs-lookup"><span data-stu-id="03057-144">Essential cookies</span></span>

<span data-ttu-id="03057-145">ストアに対する同意 cookie が提供されていない場合は、必須とマークされた cookie s のみがブラウザーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="03057-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="03057-146">次のコードでは、が cookie 不可欠です。</span><span class="sxs-lookup"><span data-stu-id="03057-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="03057-147">TempData provider と session state s は必須ではあり cookie ません</span><span class="sxs-lookup"><span data-stu-id="03057-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="03057-148">[Tempdata プロバイダー](xref:fundamentals/app-state#tempdata)は必須では cookie ありません。</span><span class="sxs-lookup"><span data-stu-id="03057-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="03057-149">追跡が無効になっている場合、TempData プロバイダーは機能しません。</span><span class="sxs-lookup"><span data-stu-id="03057-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="03057-150">追跡が無効になっているときに TempData プロバイダーを有効にするには、次のように TempData を cookie 必須としてマークし `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="03057-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="03057-151">[セッションの状態](xref:fundamentals/app-state) cookieは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="03057-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="03057-152">追跡が無効になっている場合、セッション状態は機能しません。</span><span class="sxs-lookup"><span data-stu-id="03057-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="03057-153">次のコードを使用すると、セッションが不可欠になり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="03057-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="03057-154">個人データ</span><span class="sxs-lookup"><span data-stu-id="03057-154">Personal data</span></span>

<span data-ttu-id="03057-155">個々のユーザーアカウントで作成されたアプリには、個人データをダウンロードして削除するコードが含まれています。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03057-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="03057-156">ユーザー名を選択し、[**個人データ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="03057-156">Select the user name and then select **Personal data**:</span></span>

![[個人データの管理] ページ](gdpr/_static/pd.png)

<span data-ttu-id="03057-158">メモ:</span><span class="sxs-lookup"><span data-stu-id="03057-158">Notes:</span></span>

* <span data-ttu-id="03057-159">コードを生成するには `Account/Manage` 、「[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03057-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="03057-160">[**削除**] リンクと [**ダウンロード**] リンクは、既定の id データに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="03057-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="03057-161">カスタムユーザーデータを作成するアプリは、カスタムユーザーデータを削除/ダウンロードするように拡張する必要があります。</span><span class="sxs-lookup"><span data-stu-id="03057-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="03057-162">詳細については、「[カスタムユーザーデータの追加、ダウンロード、 Identity および削除」を](xref:security/authentication/add-user-data)参照してください。</span><span class="sxs-lookup"><span data-stu-id="03057-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="03057-163">データベーステーブルに格納されているユーザーの保存済みトークン Identity `AspNetUserTokens` は、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)による連鎖削除動作によってユーザーが削除されると削除されます。</span><span class="sxs-lookup"><span data-stu-id="03057-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="03057-164">Facebook や Google などの[外部プロバイダー認証](xref:security/authentication/social/index)は、ポリシーを受け入れる前に使用できません cookie 。</span><span class="sxs-lookup"><span data-stu-id="03057-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="03057-165">保存時の暗号化</span><span class="sxs-lookup"><span data-stu-id="03057-165">Encryption at rest</span></span>

<span data-ttu-id="03057-166">一部のデータベースとストレージメカニズムでは、保存時の暗号化が可能です。</span><span class="sxs-lookup"><span data-stu-id="03057-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="03057-167">保存時の暗号化:</span><span class="sxs-lookup"><span data-stu-id="03057-167">Encryption at rest:</span></span>

* <span data-ttu-id="03057-168">保存されたデータを自動的に暗号化します。</span><span class="sxs-lookup"><span data-stu-id="03057-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="03057-169">は、データにアクセスするソフトウェアの構成、プログラミング、またはその他の作業を行わずに暗号化します。</span><span class="sxs-lookup"><span data-stu-id="03057-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="03057-170">は最も簡単で安全なオプションです。</span><span class="sxs-lookup"><span data-stu-id="03057-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="03057-171">データベースでキーと暗号化を管理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="03057-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="03057-172">例:</span><span class="sxs-lookup"><span data-stu-id="03057-172">For example:</span></span>

* <span data-ttu-id="03057-173">Microsoft SQL と Azure SQL は、 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) を提供します。</span><span class="sxs-lookup"><span data-stu-id="03057-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="03057-174">既定では、SQL Azure によってデータベースが暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="03057-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="03057-175">[既定では、Azure の blob、ファイル、テーブル、および Queue Storage が暗号化され](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)ます。</span><span class="sxs-lookup"><span data-stu-id="03057-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="03057-176">保存時の組み込みの暗号化を提供しないデータベースの場合は、ディスク暗号化を使用して同じ保護を提供できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="03057-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="03057-177">例:</span><span class="sxs-lookup"><span data-stu-id="03057-177">For example:</span></span>

* [<span data-ttu-id="03057-178">Windows Server 用の BitLocker</span><span class="sxs-lookup"><span data-stu-id="03057-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="03057-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="03057-179">Linux:</span></span>
  * [<span data-ttu-id="03057-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="03057-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="03057-181">[Encfs](https://github.com/vgough/encfs)。</span><span class="sxs-lookup"><span data-stu-id="03057-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03057-182">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="03057-182">Additional resources</span></span>

* [<span data-ttu-id="03057-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="03057-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="03057-184">GDPR-ASP.NET Core に Revoke 同意ボタンを追加する</span><span class="sxs-lookup"><span data-stu-id="03057-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
