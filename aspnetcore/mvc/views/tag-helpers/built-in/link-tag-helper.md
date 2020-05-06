---
title: ASP.NET Core のリンク タグ ヘルパー
author: rick-anderson
ms.author: riande
description: ASP.NET Core リンク タグ ヘルパーの属性と、HTML リンク タグの動作拡張時の各属性の役割を示します。
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 1efd7c1a63baea4312a4a01cd9cd9c7582375d97
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777355"
---
# <a name="link-tag-helper-in-aspnet-core"></a>ASP.NET Core のリンク タグ ヘルパー

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[リンク タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)によって、プライマリまたはフォール バック CSS ファイルへのリンクが生成されます。 通常、プライマリ CSS ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。

[!INCLUDE[](~/includes/cdn.md)]

リンク タグ ヘルパーを使用すると、CSS ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。 リンク タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。

次Razorのマークアップは`head` 、ASP.NET Core web アプリテンプレートを使用して作成されたレイアウトファイルの要素を示しています。

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

次に示すのは、前のコードからレンダリングされた HTML です (非開発環境)。

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

前のコードでは、リンク タグ ヘルパーによって `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 要素が生成され、要求された *bootstrap.min.css* ファイルが CDN 上にあることを確認するために使用される次の JavaScript も生成されました。 このケースでは、CSS ファイルは存在しており、タグ ヘルパーによって CDN CSS ファイルを含む `<link />` 要素が生成されました。

## <a name="commonly-used-link-tag-helper-attributes"></a>一般的に使用されるリンク タグ ヘルパー属性

リンク タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[リンク タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)に関するページを参照してください。

### <a name="href"></a>href

リンクされたリソースの優先アドレス。 このアドレスは、生成された HTML に常に渡されます。

### <a name="asp-fallback-href"></a>asp-fallback-href

プライマリ URL が失敗した場合にフォールバックする CSS スタイルシートの URL。

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

フォールバック テストで使用するためにスタイルシートに定義されているクラス名。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>」を参照してください。

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

フォールバック テストで使用する CSS プロパティ名。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>」を参照してください。

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

フォールバック テストで使用する CSS プロパティ値。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
