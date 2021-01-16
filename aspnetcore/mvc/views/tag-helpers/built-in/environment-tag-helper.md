---
title: ASP.NET Core の環境タグ ヘルパー
author: pkellner
description: すべてのプロパティを含む、定義済みの ASP.NET Core 環境タグ ヘルパー
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253125"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core の環境タグ ヘルパー

著者: [Peter Kellner](https://peterkellner.net)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)

環境タグヘルパーは、現在の [ホスト環境](xref:fundamentals/environments)に基づいて、囲まれたコンテンツを条件付きでレンダリングします。 環境タグ ヘルパーの 1 つの属性 `names` は、環境名のコンマ区切りリストです。 指定された環境名のいずれかが現在の環境と一致する場合、囲まれたコンテンツが表示されます。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

## <a name="environment-tag-helper-attributes"></a>環境タグ ヘルパーの属性

### <a name="names"></a>names

`names` は、囲まれたコンテンツの表示をトリガーする単一のホスティング環境名またはホスティング環境名のコンマ区切りリストを受け入れます。

環境値は、 [Iwebhostenvironment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)によって返される現在の値と比較されます。環境名。 比較では大文字と小文字の区別は無視されます。

環境タグ ヘルパーを使用する例を次に示します。 ホスティング環境が Staging または Production の場合、コンテンツが表示されます。

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include および exclude 属性

`include`& `exclude` インクルードまたは除外されるホスティング環境名に基づいて、囲まれたコンテンツをレンダリングする属性コントロール。

### <a name="include"></a>include

`include` プロパティが示す動作は、`names` 属性と似ています。 属性値に一覧表示されている環境は、 `include` タグの内容を表示するために、アプリのホスト環境 ([Iwebhostenvironment. 環境名](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) と一致する必要があり `<environment>` ます。

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>除外

`include` 属性とは対照的に、`<environment>` タグの内容は、ホスティング環境が `exclude` 属性の値で列記されている環境と一致しない場合に表示されます。

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/environments>
