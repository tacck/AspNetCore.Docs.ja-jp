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
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>ASP.NET Core での EU 一般データ保護規則 (GDPR) のサポート

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core は、一部の[EU 一般データ保護規則 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en)要件を満たすための api とテンプレートを提供します。

::: moniker range=">= aspnetcore-3.0"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、お客様のプライバシーやポリシーを使用して置き換えることができ cookie ます。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。

cookieASP.NET Core 3.0 テンプレートで生成されたアプリの ASP.NET Core 2.2 テンプレートにあるような既定の同意機能を有効にするには、次のようにします。

* `using Microsoft.AspNetCore.Http`Using ディレクティブのリストにを追加します。
* に[ Cookie policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)を追加 `Startup.ConfigureServices` し、 [ Cookie ポリシーを使用](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)して次のことを `Startup.Configure` 行います。

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* cookie同意部分を *_Layout*ファイルに追加します。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* * \_ Cookie Conの部分的*なファイルをプロジェクトに追加します。

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* 同意機能については、この記事の ASP.NET Core 2.2 バージョンを参照して cookie ください。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* プロジェクトテンプレートには、拡張ポイントとスタブマークアップが含まれています。これは、お客様のプライバシーやポリシーを使用して置き換えることができ cookie ます。
* 同意機能を使用すると、 cookie 個人情報の保存をユーザーに要求 (および追跡) することができます。 ユーザーがデータ収集に同意しておらず、アプリの[Checkcon](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded)がに設定されている場合 `true` 、非重要なは cookie ブラウザーに送信されません。
* Cookieは、必須としてマークできます。 必須 cookie のは、ユーザーが同意しておらず、追跡が無効になっている場合でも、ブラウザーに送信されます。
* 追跡が無効になっている場合[、Tempdata と Session cookie s](#tempdata)は機能しません。
* [ [ Identity 管理](#pd)] ページには、ユーザーデータをダウンロードおよび削除するためのリンクが用意されています。

この[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)を使用すると、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと api の大部分をテストできます。 テスト手順については、 [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)ファイルを参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>テンプレートで生成されたコードでの GDPR サポートの ASP.NET Core

Razorプロジェクトテンプレートで作成されたページと MVC プロジェクトには、次の GDPR サポートが含まれます。

* [ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)と[Use Cookie policy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は、クラスで設定され `Startup` ます。
* * \_ Cookie Conの*部分的な部分[ビュー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。 このファイルには、[ **Accept** ] ボタンが含まれています。 ユーザーが [**同意**する] ボタンをクリックすると、store s に同意したことに cookie なります。
* *Pages/privacy. cshtml*ページまたは*Views/Home/privacy*ビューには、サイトのプライバシーポリシーを詳細に説明するページが用意されています。 * \_ Cookie Con/partial*ファイルは、プライバシーページへのリンクを生成します。
* 個々のユーザーアカウントで作成されたアプリの場合、[管理] ページに、[個人ユーザーデータ](#pd)をダウンロードおよび削除するためのリンクが表示されます。

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions と Use Cookie policy

[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions)は次のもので初期化され `Startup.ConfigureServices` ます。

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[使用 Cookieポリシー](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)は次のもので呼び出され `Startup.Configure` ます。

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieConの部分的な部分ビューの表示

* \_ Cookie Conの*部分的な部分ビューは次のようになります。

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

この部分は次のとおりです。

* ユーザーの追跡の状態を取得します。 アプリが同意を要求するように構成されている場合、ユーザーはを追跡する前に同意する必要があり cookie ます。 同意が必要な場合は、 cookie * \_ Layout*ファイルによって作成されたナビゲーションバーの上部に同意パネルが固定されます。
* `<p>`プライバシーを要約し、ポリシーを使用するための HTML 要素が用意されて cookie います。
* サイトのプライバシーポリシーを詳細に説明する [プライバシー] ページまたはビューへのリンクを提供します。

## <a name="essential-no-loccookies"></a>必須 cookie

ストアに対する同意 cookie が提供されていない場合は、必須とマークされた cookie s のみがブラウザーに送信されます。 次のコードでは、が cookie 不可欠です。

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>TempData provider と session state s は必須ではあり cookie ません

[Tempdata プロバイダー](xref:fundamentals/app-state#tempdata)は必須では cookie ありません。 追跡が無効になっている場合、TempData プロバイダーは機能しません。 追跡が無効になっているときに TempData プロバイダーを有効にするには、次のように TempData を cookie 必須としてマークし `Startup.ConfigureServices` ます。

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[セッションの状態](xref:fundamentals/app-state) cookieは必須ではありません。 追跡が無効になっている場合、セッション状態は機能しません。 次のコードを使用すると、セッションが不可欠になり cookie ます。

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>個人データ

個々のユーザーアカウントで作成されたアプリには、個人データをダウンロードして削除するコードが含まれています。 ASP.NET Core

ユーザー名を選択し、[**個人データ**] を選択します。

![[個人データの管理] ページ](gdpr/_static/pd.png)

メモ:

* コードを生成するには `Account/Manage` 、「[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)」を参照してください。
* [**削除**] リンクと [**ダウンロード**] リンクは、既定の id データに対してのみ機能します。 カスタムユーザーデータを作成するアプリは、カスタムユーザーデータを削除/ダウンロードするように拡張する必要があります。 詳細については、「[カスタムユーザーデータの追加、ダウンロード、 Identity および削除」を](xref:security/authentication/add-user-data)参照してください。
* データベーステーブルに格納されているユーザーの保存済みトークン Identity `AspNetUserTokens` は、[外部キー](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)による連鎖削除動作によってユーザーが削除されると削除されます。
* Facebook や Google などの[外部プロバイダー認証](xref:security/authentication/social/index)は、ポリシーを受け入れる前に使用できません cookie 。

::: moniker-end

## <a name="encryption-at-rest"></a>保存時の暗号化

一部のデータベースとストレージメカニズムでは、保存時の暗号化が可能です。 保存時の暗号化:

* 保存されたデータを自動的に暗号化します。
* は、データにアクセスするソフトウェアの構成、プログラミング、またはその他の作業を行わずに暗号化します。
* は最も簡単で安全なオプションです。
* データベースでキーと暗号化を管理できるようにします。

例:

* Microsoft SQL と Azure SQL は、 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) を提供します。
* [既定では、SQL Azure によってデータベースが暗号化されます。](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [既定では、Azure の blob、ファイル、テーブル、および Queue Storage が暗号化され](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)ます。

保存時の組み込みの暗号化を提供しないデータベースの場合は、ディスク暗号化を使用して同じ保護を提供できる場合があります。 例:

* [Windows Server 用の BitLocker](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs)。

## <a name="additional-resources"></a>その他のリソース

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-ASP.NET Core に Revoke 同意ボタンを追加する](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
