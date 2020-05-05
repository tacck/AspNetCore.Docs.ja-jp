---
title: ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor
author: guardrex
description: ASP.NET Core Blazorアプリでコンテンツセキュリティポリシー (CSP) を使用して、クロスサイトスクリプティング (XSS) 攻撃から保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775584"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="a0742-103">ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor</span><span class="sxs-lookup"><span data-stu-id="a0742-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="a0742-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a0742-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a0742-105">[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)は、攻撃者が1つまたは複数の悪意のあるクライアント側スクリプトをアプリのレンダリングされたコンテンツに配置するセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="a0742-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="a0742-106">コンテンツセキュリティポリシー (CSP) は、ブラウザーに有効なことを通知することで、XSS 攻撃から保護するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a0742-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="a0742-107">スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。</span><span class="sxs-lookup"><span data-stu-id="a0742-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="a0742-108">ページによって実行されるアクション。フォームの許可された URL ターゲットを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="a0742-109">読み込むことができるプラグイン。</span><span class="sxs-lookup"><span data-stu-id="a0742-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="a0742-110">CSP をアプリに適用するために、開発者は1つ以上*directives*の`Content-Security-Policy`ヘッダーまた`<meta>`はタグに csp コンテンツのセキュリティディレクティブをいくつか指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="a0742-111">ポリシーは、ページの読み込み中にブラウザーによって評価されます。</span><span class="sxs-lookup"><span data-stu-id="a0742-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="a0742-112">ブラウザーはページのソースを検査し、コンテンツセキュリティディレクティブの要件を満たしているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="a0742-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="a0742-113">リソースのポリシーディレクティブが満たされていない場合、ブラウザーはリソースを読み込みません。</span><span class="sxs-lookup"><span data-stu-id="a0742-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="a0742-114">たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a0742-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="a0742-115">属性にサードパーティの`<script>`元のタグが含まれている場合は、ブラウザーによってスクリプトの読み込みが禁止されます。 `src`</span><span class="sxs-lookup"><span data-stu-id="a0742-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="a0742-116">CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイルブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0742-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="a0742-117">アプリには CSP Blazorを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0742-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="a0742-118">ポリシーディレクティブ</span><span class="sxs-lookup"><span data-stu-id="a0742-118">Policy directives</span></span>

<span data-ttu-id="a0742-119">最小で、次のアプリのBlazorディレクティブとソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="a0742-120">必要に応じて、ディレクティブとソースを追加します。</span><span class="sxs-lookup"><span data-stu-id="a0742-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="a0742-121">この記事の「[ポリシーの適用](#apply-the-policy)」セクションでは、次のディレクティブを使用していBlazorます。ここではBlazor 、webassembly サーバーのセキュリティポリシーの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="a0742-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="a0742-122">[base uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash;は、ページの`<base>`タグの url を制限します。</span><span class="sxs-lookup"><span data-stu-id="a0742-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="a0742-123">を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="a0742-124">[すべてのブロック-混合コンテンツ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash;は、混合 HTTP と HTTPS コンテンツを読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="a0742-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="a0742-125">[既定値-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash;は、ポリシーによって明示的に指定されていないソースディレクティブのフォールバックを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="a0742-126">を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="a0742-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash;は、イメージの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="a0742-128">Url `data:`からのイメージの`data:`読み込みを許可するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="a0742-129">HTTPS `https:`エンドポイントからのイメージの読み込みを許可するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="a0742-130">[オブジェクト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash;は`<object>`、、 `<embed>`、および`<applet>`タグの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="a0742-131">すべて`none`の URL ソースを禁止するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="a0742-132">[スクリプト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash;は、スクリプトの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="a0742-133">ブートストラップスクリプト`https://stackpath.bootstrapcdn.com/`のホストソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="a0742-134">を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="a0742-135">Webassembly の場合Blazor :</span><span class="sxs-lookup"><span data-stu-id="a0742-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="a0742-136">読み込む必要Blazorがあるインラインスクリプトを許可するには、次のハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="a0742-137">文字列`unsafe-eval`からコード`eval()`を作成するためにメソッドとメソッドを使用するように指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="a0742-138">Blazorサーバーアプリで、スタイルシートの`sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=`フォールバック検出を実行するインラインスクリプトのハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="a0742-139">[スタイル-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash;は、スタイルシートの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="a0742-140">ブートストラップスタイル`https://stackpath.bootstrapcdn.com/`シートのホストソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="a0742-141">を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="a0742-142">インライン`unsafe-inline`スタイルの使用を許可するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="a0742-143">初期要求後にクライアントとサーバーを再Blazor接続するために、サーバーアプリの UI でインライン宣言が必要です。</span><span class="sxs-lookup"><span data-stu-id="a0742-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="a0742-144">今後のリリースで`unsafe-inline`は、が不要になるように、インラインスタイルが削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0742-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="a0742-145">[アップグレード-安全で](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash;はない要求は、セキュリティで保護されていない (HTTP) ソースからのコンテンツ url を HTTPS 経由で安全に取得する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="a0742-146">上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0742-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="a0742-147">追加のインラインスクリプトの SHA ハッシュを取得するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0742-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="a0742-148">「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="a0742-149">アプリをローカルで実行しながら、ブラウザーの開発者ツールコンソールにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="a0742-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="a0742-150">CSP ヘッダーまたは`meta`タグが存在する場合、ブラウザーはブロックされたスクリプトのハッシュを計算して表示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="a0742-151">ブラウザーによって提供されるハッシュを`script-src`ソースにコピーします。</span><span class="sxs-lookup"><span data-stu-id="a0742-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="a0742-152">各ハッシュを囲む単一引用符を使用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="a0742-153">コンテンツセキュリティポリシーレベル2のブラウザーサポートマトリックスについては、「[使用可能: コンテンツセキュリティポリシーレベル 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0742-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="a0742-154">ポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="a0742-154">Apply the policy</span></span>

<span data-ttu-id="a0742-155">ポリシーを`<meta>`適用するには、タグを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="a0742-156">`http-equiv`属性の値をに`Content-Security-Policy`設定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="a0742-157">`content`属性値にディレクティブを配置します。</span><span class="sxs-lookup"><span data-stu-id="a0742-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="a0742-158">ディレクティブはセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="a0742-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="a0742-159">常にタグ`meta`を`<head>`コンテンツに配置します。</span><span class="sxs-lookup"><span data-stu-id="a0742-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="a0742-160">以下のセクションでは、webassembly Blazor Blazorサーバーのポリシーの例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0742-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="a0742-161">これらの例は、のBlazor各リリースについて、この記事でバージョン管理されています。</span><span class="sxs-lookup"><span data-stu-id="a0742-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="a0742-162">リリースに適したバージョンを使用するには、この web ページの**バージョン**ドロップダウンセレクターでドキュメントバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="a0742-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="a0742-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a0742-163"> WebAssembly</span></span>

<span data-ttu-id="a0742-164">Wwwroot/index.html ホスト`<head>`ページのコンテンツ*wwwroot/index.html*で、「[ポリシーディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="a0742-165"> サーバー</span><span class="sxs-lookup"><span data-stu-id="a0742-165"> Server</span></span>

<span data-ttu-id="a0742-166">`<head>` *Pages/_Host*の [ホスト] ページの内容で、[[ポリシーディレクティブ](#policy-directives)] セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="a0742-167">Meta タグの制限事項</span><span class="sxs-lookup"><span data-stu-id="a0742-167">Meta tag limitations</span></span>

<span data-ttu-id="a0742-168">タグ`<meta>`ポリシーは、次のディレクティブをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="a0742-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="a0742-169">フレーム-先祖</span><span class="sxs-lookup"><span data-stu-id="a0742-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="a0742-170">レポート先</span><span class="sxs-lookup"><span data-stu-id="a0742-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="a0742-171">レポート-uri</span><span class="sxs-lookup"><span data-stu-id="a0742-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="a0742-172">サンドボックス</span><span class="sxs-lookup"><span data-stu-id="a0742-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="a0742-173">上記のディレクティブをサポートするには、と`Content-Security-Policy`いう名前のヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0742-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="a0742-174">ディレクティブ文字列は、ヘッダーの値です。</span><span class="sxs-lookup"><span data-stu-id="a0742-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="a0742-175">ポリシーをテストして違反レポートを受信する</span><span class="sxs-lookup"><span data-stu-id="a0742-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="a0742-176">テストは、初期ポリシーを作成するときに、サードパーティ製のスクリプトが誤ってブロックされていないことを確認するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a0742-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="a0742-177">ポリシーディレクティブを適用せずに一定期間にわたってポリシーをテストするに`<meta>`は、 `http-equiv`ヘッダーベースのポリシーのタグの属性またはヘッダー `Content-Security-Policy-Report-Only`名をに設定します。</span><span class="sxs-lookup"><span data-stu-id="a0742-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="a0742-178">エラーレポートは、JSON ドキュメントとして指定された URL に送信されます。</span><span class="sxs-lookup"><span data-stu-id="a0742-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="a0742-179">詳細については、 [MDN web ドキュメント: コンテンツ-セキュリティポリシー-レポートのみ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0742-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="a0742-180">ポリシーがアクティブになっているときの違反を報告するには、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0742-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="a0742-181">レポート先</span><span class="sxs-lookup"><span data-stu-id="a0742-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="a0742-182">レポート-uri</span><span class="sxs-lookup"><span data-stu-id="a0742-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="a0742-183">の`report-uri`使用は推奨されなくなりましたが、すべての`report-to`主要なブラウザーでがサポートされるまでは、両方のディレクティブを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0742-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="a0742-184">を排他的`report-uri`に使用し`report-uri`ないでください。のサポートは、ブラウザーからいつ*でも*削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0742-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="a0742-185">が完全に`report-uri`サポートされて`report-to`いる場合は、ポリシー内ののサポートを削除します。</span><span class="sxs-lookup"><span data-stu-id="a0742-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="a0742-186">の`report-to`導入を追跡する方法については、「[レポートを使用](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)するには」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0742-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="a0742-187">アプリのポリシーをリリースするたびにテストして更新します。</span><span class="sxs-lookup"><span data-stu-id="a0742-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a0742-188">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="a0742-188">Troubleshoot</span></span>

* <span data-ttu-id="a0742-189">エラーは、ブラウザーの開発者ツールコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0742-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="a0742-190">ブラウザーは次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="a0742-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="a0742-191">ポリシーに準拠していない要素。</span><span class="sxs-lookup"><span data-stu-id="a0742-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="a0742-192">ブロックされた項目を許可するようにポリシーを変更する方法。</span><span class="sxs-lookup"><span data-stu-id="a0742-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="a0742-193">ポリシーが完全に有効になるのは、クライアントのブラウザーが含まれているすべてのディレクティブをサポートしている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="a0742-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="a0742-194">現在のブラウザーのサポートマトリックスについては、「[使用可能: コンテンツ-セキュリティ-ポリシー](https://caniuse.com/#search=Content-Security-Policy)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0742-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0742-195">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a0742-195">Additional resources</span></span>

* [<span data-ttu-id="a0742-196">MDN web ドキュメント: コンテンツ-セキュリティ-ポリシー</span><span class="sxs-lookup"><span data-stu-id="a0742-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="a0742-197">コンテンツセキュリティポリシーレベル2</span><span class="sxs-lookup"><span data-stu-id="a0742-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="a0742-198">Google CSP エバリュエーター</span><span class="sxs-lookup"><span data-stu-id="a0742-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
