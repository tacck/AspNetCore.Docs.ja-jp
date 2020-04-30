---
title: ASP.NET Core Blazor サーバー アプリをセキュリティで保護する
author: guardrex
description: Blazor Server アプリを ASP.NET Core アプリケーションとしてセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206330"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>ASP.NET Core Blazor サーバー アプリをセキュリティで保護する

作成者: [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Blazor サーバー プロジェクト テンプレート

Blazor サーバー プロジェクト テンプレートは、プロジェクトの作成時に認証を構成できます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。

**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。

ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。

* **認証なし**
* **個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。
  * ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c)。
* **職場または学校アカウント**
* **Windows 認証**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム | 説明 |
| ------------------------ | ----------- |
| `None` (既定値)         | 認証なし |
| `Individual`             | ASP.NET Core ID を使用してアプリに格納されているユーザー |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー |
| `SingleOrg`              | 単一のテナントに対する組織認証 |
| `MultiOrg`               | 複数のテナントに対する組織認証 |
| `Windows`                | Windows 認証 |

`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。

* プロジェクトのフォルダーを作成します。
* プロジェクトに名前を付けます。

詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. <xref:blazor/get-started> の記事に記載されている Visual Studio for Mac のガイダンスに従ってください。

1. **[Configure your new Blazor Server App]\(新しい Blazor サーバー アプリの構成\)** ステップで、 **[認証]** ドロップダウンから **[Individual Authentication (in-app)]\(個別認証 (アプリ内)\)** を選択します。

1. ASP.NET Core ID を使用してアプリに格納されている個々のユーザーに対して、アプリが作成されます。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事に記載されている .NET Core CLI のガイダンスに従ってください。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム | 説明 |
| ------------------------ | ----------- |
| `None` (既定値)         | 認証なし |
| `Individual`             | ASP.NET Core ID を使用してアプリに格納されているユーザー |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー |
| `SingleOrg`              | 単一のテナントに対する組織認証 |
| `MultiOrg`               | 複数のテナントに対する組織認証 |
| `Windows`                | Windows 認証 |

`-o|--output` オプションを指定してコマンドを実行すると、`{APP NAME}` プレースホルダーに指定した値を使用して次が実行されます。

* プロジェクトのフォルダーを作成します。
* プロジェクトに名前を付けます。

詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。

---
