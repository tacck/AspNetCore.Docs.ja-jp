---
title: ASP.NET Core Blazor のコンテンツ セキュリティ ポリシーを適用する
author: guardrex
description: ASP.NET Core Blazor アプリでコンテンツ セキュリティ ポリシー (CSP) を使用して、クロスサイト スクリプティング (XSS) 攻撃から保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 5c53ac64d3ae1b365b40c519eb119f913d58cad1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402443"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="3fd29-103">ASP.NET Core Blazor のコンテンツ セキュリティ ポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="3fd29-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3fd29-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3fd29-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3fd29-105">[クロスサイト スクリプティング (XSS)](xref:security/cross-site-scripting) は、アプリのレンダリングされたコンテンツに攻撃者が 1 つ以上の悪意のあるクライアント側スクリプトを配置するセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="3fd29-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="3fd29-106">コンテンツ セキュリティ ポリシー (CSP) は、ブラウザーに次の有効な内容を通知することで、XSS 攻撃から保護することができます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="3fd29-107">スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。</span><span class="sxs-lookup"><span data-stu-id="3fd29-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="3fd29-108">ページによって実行され、フォームの許可された URL ターゲットを指定するアクション。</span><span class="sxs-lookup"><span data-stu-id="3fd29-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="3fd29-109">読み込むことができるプラグイン。</span><span class="sxs-lookup"><span data-stu-id="3fd29-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="3fd29-110">CSP をアプリに適用するために、開発者は 1 つ以上の `Content-Security-Policy` ヘッダーまたは `<meta>` タグに複数の CSP コンテンツ セキュリティ "*ディレクティブ*" を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="3fd29-111">ポリシーは、ページの読み込み中にブラウザーによって評価されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="3fd29-112">ブラウザーによりページのソースが検査され、コンテンツ セキュリティ ディレクティブの要件を満たしているかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="3fd29-113">リソースのポリシー ディレクティブが満たされていない場合、ブラウザーでリソースが読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="3fd29-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="3fd29-114">たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="3fd29-115">ページの `src` 属性にサードパーティから発生した `<script>` タグが含まれている場合、ブラウザーによってスクリプトの読み込みが禁止されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="3fd29-116">CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイル ブラウザーのほとんどでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3fd29-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="3fd29-117">CSP は、Blazor アプリで推奨されています。</span><span class="sxs-lookup"><span data-stu-id="3fd29-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="3fd29-118">ポリシー ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="3fd29-118">Policy directives</span></span>

<span data-ttu-id="3fd29-119">最小限として、Blazor アプリの次のディレクティブとソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="3fd29-120">必要に応じて、ディレクティブとソースを追加してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="3fd29-121">次のディレクティブは、この記事の「[ポリシーの適用](#apply-the-policy)」セクションで使用されます。そこでは、Blazor WebAssembly と Blazor Server のセキュリティ ポリシーの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="3fd29-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="3fd29-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ページの `<base>` タグの URL を制限します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="3fd29-123">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="3fd29-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): HTTP と HTTPS の混合コンテンツの読み込みを禁止します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="3fd29-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): ポリシーによって明示的に指定されていないソース ディレクティブのフォールバックを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="3fd29-126">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="3fd29-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): 画像の有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="3fd29-128">`data:` の URL からの画像の読み込みを許可するには `data:` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="3fd29-129">HTTPS のエンドポイントからの画像の読み込みを許可するには `https:` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="3fd29-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>`、`<embed>`、`<applet>` タグの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="3fd29-131">すべての URL ソースを禁止するには、`none` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="3fd29-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): スクリプトの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="3fd29-133">ブートストラップ スクリプトの `https://stackpath.bootstrapcdn.com/` ホスト ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="3fd29-134">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="3fd29-135">Blazor WebAssembly アプリでは以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="3fd29-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="3fd29-136">次のハッシュを指定して、必要な Blazor WebAssembly のインライン スクリプトの読み込みを許可します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="3fd29-137">`eval()` および文字列からコードを作成するメソッドを使用するには、`unsafe-eval` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="3fd29-138">Blazor Server アプリでは、スタイルシートのフォールバック検出を実行するインライン スクリプトの `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` ハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="3fd29-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): スタイルシートの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="3fd29-140">ブートストラップ スタイルシートの `https://stackpath.bootstrapcdn.com/` ホスト ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="3fd29-141">`self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="3fd29-142">インライン スタイルの使用を許可するには `unsafe-inline` を指定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="3fd29-143">初期要求後にクライアントとサーバーを再接続するには、Blazor Server アプリの UI にインライン宣言が必要です。</span><span class="sxs-lookup"><span data-stu-id="3fd29-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="3fd29-144">今後のリリースでは、`unsafe-inline` が不要になるように、インライン スタイルが削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3fd29-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="3fd29-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): セキュリティで保護されていない (HTTP) ソースからのコンテンツ URL を HTTPS 経由で安全に取得する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="3fd29-146">上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="3fd29-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="3fd29-147">追加のインライン スクリプトの SHA ハッシュを取得するには:</span><span class="sxs-lookup"><span data-stu-id="3fd29-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="3fd29-148">「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="3fd29-149">アプリをローカルで実行しながら、ブラウザーの開発者ツールのコンソールにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3fd29-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="3fd29-150">CSP ヘッダーまたは `meta` タグが存在する場合、ブラウザーではブロックされたスクリプトのハッシュが計算されて表示されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="3fd29-151">ブラウザーによって提供されるハッシュを `script-src` ソースにコピーします。</span><span class="sxs-lookup"><span data-stu-id="3fd29-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="3fd29-152">単一引用符を使用して各ハッシュを囲みます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="3fd29-153">コンテンツ セキュリティ ポリシー レベル 2 のブラウザー サポート マトリックスは、「[Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」(私は使用できますか? コンテンツ セキュリティ ポリシー レベル 2) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="3fd29-154">ポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="3fd29-154">Apply the policy</span></span>

<span data-ttu-id="3fd29-155">`<meta>` タグを使用してポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="3fd29-156">`http-equiv` 属性の値は `Content-Security-Policy` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="3fd29-157">`content` 属性値にディレクティブを配置します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="3fd29-158">各ディレクティブをセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="3fd29-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="3fd29-159">常に `meta` タグを `<head>` のコンテンツに配置します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="3fd29-160">次のセクションに、Blazor WebAssembly と Blazor Server のポリシーの例を示します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="3fd29-161">これらの例は、Blazor のリリースごとにこの記事でバージョン管理されています。</span><span class="sxs-lookup"><span data-stu-id="3fd29-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="3fd29-162">リリースに適したバージョンを使用するには、この Web ページの **[バージョン]** ドロップ ダウン セレクターを使用してドキュメントのバージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="3fd29-163">`wwwroot/index.html` ホスト ページの `<head>` コンテンツで、「[ポリシー ディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### Blazor Server

<span data-ttu-id="3fd29-164">`Pages/_Host.cshtml` ホスト ページの `<head>` コンテンツで、「[ポリシー ディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="3fd29-165">Meta タグに関する制限事項</span><span class="sxs-lookup"><span data-stu-id="3fd29-165">Meta tag limitations</span></span>

<span data-ttu-id="3fd29-166">`<meta>` タグ ポリシーでは、次のディレクティブがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3fd29-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="3fd29-167">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="3fd29-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="3fd29-168">report-to</span><span class="sxs-lookup"><span data-stu-id="3fd29-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="3fd29-169">report-uri</span><span class="sxs-lookup"><span data-stu-id="3fd29-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="3fd29-170">sandbox</span><span class="sxs-lookup"><span data-stu-id="3fd29-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="3fd29-171">上記のディレクティブをサポートするには、`Content-Security-Policy` という名前のヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="3fd29-172">ディレクティブ文字列は、ヘッダーの値です。</span><span class="sxs-lookup"><span data-stu-id="3fd29-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="3fd29-173">ポリシーをテストして違反レポートを受信する</span><span class="sxs-lookup"><span data-stu-id="3fd29-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="3fd29-174">テストは、初期ポリシーを作成するときに、サードパーティのスクリプトが誤ってブロックされていないことを確認するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="3fd29-175">ポリシー ディレクティブを適用せずに一定期間にわたってポリシーをテストするには、ヘッダーベースのポリシーの `<meta>` タグの `http-equiv` 属性またはヘッダー名を `Content-Security-Policy-Report-Only` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="3fd29-176">エラー レポートは、JSON ドキュメントとして指定された URL に送信されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="3fd29-177">詳細については、[MDN Web ドキュメント:「Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="3fd29-178">ポリシーがアクティブになっている間に違反を報告するには、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="3fd29-179">report-to</span><span class="sxs-lookup"><span data-stu-id="3fd29-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="3fd29-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="3fd29-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="3fd29-181">`report-uri` の使用は推奨されなくなりましたが、すべての主要なブラウザーで `report-to` がサポートされるようになるまで、両方のディレクティブを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3fd29-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="3fd29-182">`report-uri` のサポートは、ブラウザーから "*常に*" 削除される可能性があるため、`report-uri` を排他的に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="3fd29-183">`report-to` が完全にサポートされたら、ポリシー内の `report-uri` のサポートを削除します。</span><span class="sxs-lookup"><span data-stu-id="3fd29-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="3fd29-184">`report-to` が導入されたかどうかを追跡する方法については、「[Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)」(私は使用できますか? report-to) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="3fd29-185">アプリがリリースされるたびにポリシーをテストして更新してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3fd29-186">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3fd29-186">Troubleshoot</span></span>

* <span data-ttu-id="3fd29-187">エラーは、ブラウザーの開発者ツールのコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="3fd29-188">ブラウザーには次に関する情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3fd29-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="3fd29-189">ポリシーに準拠していない要素。</span><span class="sxs-lookup"><span data-stu-id="3fd29-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="3fd29-190">ブロックされた項目を許可するようにポリシーを変更する方法。</span><span class="sxs-lookup"><span data-stu-id="3fd29-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="3fd29-191">ポリシーが完全に有効になるのは、含まれているすべてのディレクティブがクライアントのブラウザーでサポートされている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="3fd29-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="3fd29-192">現在のブラウザーのサポート マトリックスについては、「[Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)」(私は使用できますか? Content-Security-Policy) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3fd29-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3fd29-193">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3fd29-193">Additional resources</span></span>

* [<span data-ttu-id="3fd29-194">MDN Web ドキュメント:Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="3fd29-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="3fd29-195">コンテンツ セキュリティ ポリシー レベル 2</span><span class="sxs-lookup"><span data-stu-id="3fd29-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="3fd29-196">Google CSP Evaluator</span><span class="sxs-lookup"><span data-stu-id="3fd29-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)