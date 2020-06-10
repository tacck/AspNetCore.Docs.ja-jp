---
title: ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor
author: guardrex
description: ASP.NET Core アプリでコンテンツセキュリティポリシー (CSP) を使用し Blazor て、クロスサイトスクリプティング (XSS) 攻撃から保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8615b199373ca856c252b9f843e3635770367e4a
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106391"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="9aa27-103">ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor</span><span class="sxs-lookup"><span data-stu-id="9aa27-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="9aa27-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9aa27-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9aa27-105">[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)は、攻撃者が1つまたは複数の悪意のあるクライアント側スクリプトをアプリのレンダリングされたコンテンツに配置するセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="9aa27-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="9aa27-106">コンテンツセキュリティポリシー (CSP) は、ブラウザーに有効なことを通知することで、XSS 攻撃から保護するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="9aa27-107">スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。</span><span class="sxs-lookup"><span data-stu-id="9aa27-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="9aa27-108">ページによって実行されるアクション。フォームの許可された URL ターゲットを指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="9aa27-109">読み込むことができるプラグイン。</span><span class="sxs-lookup"><span data-stu-id="9aa27-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="9aa27-110">CSP をアプリに適用するために、開発者は1つ以上のヘッダーまたはタグに CSP コンテンツのセキュリティ*ディレクティブ*をいくつか指定し `Content-Security-Policy` `<meta>` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="9aa27-111">ポリシーは、ページの読み込み中にブラウザーによって評価されます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="9aa27-112">ブラウザーはページのソースを検査し、コンテンツセキュリティディレクティブの要件を満たしているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="9aa27-113">リソースのポリシーディレクティブが満たされていない場合、ブラウザーはリソースを読み込みません。</span><span class="sxs-lookup"><span data-stu-id="9aa27-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="9aa27-114">たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="9aa27-115">属性にサードパーティの元のタグが含まれている場合は、 `<script>` `src` ブラウザーによってスクリプトの読み込みが禁止されます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="9aa27-116">CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイルブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9aa27-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="9aa27-117">アプリには CSP を使用することをお勧め Blazor します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="9aa27-118">ポリシーディレクティブ</span><span class="sxs-lookup"><span data-stu-id="9aa27-118">Policy directives</span></span>

<span data-ttu-id="9aa27-119">最小で、次のアプリのディレクティブとソースを指定し Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="9aa27-120">必要に応じて、ディレクティブとソースを追加します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="9aa27-121">この記事の「[ポリシーの適用](#apply-the-policy)」セクションでは、次のディレクティブを使用しています。ここでは、webassembly サーバーのセキュリティポリシーの例を示し Blazor Blazor ています。</span><span class="sxs-lookup"><span data-stu-id="9aa27-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="9aa27-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ページのタグの url を制限 `<base>` します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="9aa27-123">を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="9aa27-124">[all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): 混合 HTTP と HTTPS コンテンツの読み込みを禁止します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="9aa27-125">[既定値-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): ポリシーで明示的に指定されていないソースディレクティブのフォールバックを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="9aa27-126">を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="9aa27-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): イメージの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="9aa27-128">`data:`Url からのイメージの読み込みを許可するには、を指定し `data:` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="9aa27-129">`https:`HTTPS エンドポイントからのイメージの読み込みを許可するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="9aa27-130">[オブジェクト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>` 、、およびタグの有効なソースを示し `<embed>` `<applet>` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="9aa27-131">`none`すべての URL ソースを禁止するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="9aa27-132">[スクリプト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): スクリプトの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="9aa27-133">`https://stackpath.bootstrapcdn.com/`ブートストラップスクリプトのホストソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="9aa27-134">を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="9aa27-135">BlazorWebassembly の場合:</span><span class="sxs-lookup"><span data-stu-id="9aa27-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="9aa27-136">読み込む必要があるインラインスクリプトを許可するには、次のハッシュを指定し Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="9aa27-137">`unsafe-eval`文字列からコードを作成するためにメソッドとメソッドを使用するように指定し `eval()` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="9aa27-138">Blazorサーバーアプリで、 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` スタイルシートのフォールバック検出を実行するインラインスクリプトのハッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="9aa27-139">[スタイル-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): スタイルシートの有効なソースを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="9aa27-140">`https://stackpath.bootstrapcdn.com/`ブートストラップスタイルシートのホストソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="9aa27-141">を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="9aa27-142">`unsafe-inline`インラインスタイルの使用を許可するには、を指定します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="9aa27-143">Blazor初期要求後にクライアントとサーバーを再接続するために、サーバーアプリの UI でインライン宣言が必要です。</span><span class="sxs-lookup"><span data-stu-id="9aa27-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="9aa27-144">今後のリリースでは、が不要になるように、インラインスタイルが削除される可能性があり `unsafe-inline` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="9aa27-145">[upgrade-セキュリティで](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)保護されていない要求: セキュリティで保護されていない (HTTP) ソースからのコンテンツ URL を HTTPS 経由で安全に取得する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="9aa27-146">上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9aa27-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="9aa27-147">追加のインラインスクリプトの SHA ハッシュを取得するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9aa27-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="9aa27-148">「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="9aa27-149">アプリをローカルで実行しながら、ブラウザーの開発者ツールコンソールにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9aa27-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="9aa27-150">CSP ヘッダーまたはタグが存在する場合、ブラウザーはブロックされたスクリプトのハッシュを計算して表示し `meta` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="9aa27-151">ブラウザーによって提供されるハッシュをソースにコピーし `script-src` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="9aa27-152">各ハッシュを囲む単一引用符を使用します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="9aa27-153">コンテンツセキュリティポリシーレベル2のブラウザーサポートマトリックスについては、「[使用可能: コンテンツセキュリティポリシーレベル 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9aa27-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="9aa27-154">ポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="9aa27-154">Apply the policy</span></span>

<span data-ttu-id="9aa27-155">ポリシーを `<meta>` 適用するには、タグを使用します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="9aa27-156">属性の値 `http-equiv` をに設定 `Content-Security-Policy` します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="9aa27-157">属性値にディレクティブを配置 `content` します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="9aa27-158">ディレクティブはセミコロン () で区切り `;` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="9aa27-159">常に `meta` タグをコンテンツに配置 `<head>` します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="9aa27-160">以下のセクションでは、webassembly サーバーのポリシーの例を示し Blazor Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="9aa27-161">これらの例は、の各リリースについて、この記事でバージョン管理されてい Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="9aa27-162">リリースに適したバージョンを使用するには、この web ページの**バージョン**ドロップダウンセレクターでドキュメントバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="9aa27-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9aa27-163"> WebAssembly</span></span>

<span data-ttu-id="9aa27-164">`<head>` *Wwwroot/index.html*ホストページのコンテンツで、「[ポリシーディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="9aa27-165"> サーバー</span><span class="sxs-lookup"><span data-stu-id="9aa27-165"> Server</span></span>

<span data-ttu-id="9aa27-166">`<head>` *Pages/_Host*の [ホスト] ページの内容で、[[ポリシーディレクティブ](#policy-directives)] セクションで説明されているディレクティブを適用します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="9aa27-167">Meta タグの制限事項</span><span class="sxs-lookup"><span data-stu-id="9aa27-167">Meta tag limitations</span></span>

<span data-ttu-id="9aa27-168">`<meta>`タグポリシーは、次のディレクティブをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9aa27-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="9aa27-169">フレーム-先祖</span><span class="sxs-lookup"><span data-stu-id="9aa27-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="9aa27-170">レポート先</span><span class="sxs-lookup"><span data-stu-id="9aa27-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="9aa27-171">レポート-uri</span><span class="sxs-lookup"><span data-stu-id="9aa27-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="9aa27-172">サンドボックス</span><span class="sxs-lookup"><span data-stu-id="9aa27-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="9aa27-173">上記のディレクティブをサポートするには、という名前のヘッダーを使用し `Content-Security-Policy` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="9aa27-174">ディレクティブ文字列は、ヘッダーの値です。</span><span class="sxs-lookup"><span data-stu-id="9aa27-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="9aa27-175">ポリシーをテストして違反レポートを受信する</span><span class="sxs-lookup"><span data-stu-id="9aa27-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="9aa27-176">テストは、初期ポリシーを作成するときに、サードパーティ製のスクリプトが誤ってブロックされていないことを確認するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="9aa27-177">ポリシーディレクティブを適用せずに一定期間にわたってポリシーをテストするには、 `<meta>` `http-equiv` ヘッダーベースのポリシーのタグの属性またはヘッダー名をに設定し `Content-Security-Policy-Report-Only` ます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="9aa27-178">エラーレポートは、JSON ドキュメントとして指定された URL に送信されます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="9aa27-179">詳細については、 [MDN web ドキュメント: コンテンツ-セキュリティポリシー-レポートのみ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9aa27-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="9aa27-180">ポリシーがアクティブになっているときの違反を報告するには、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9aa27-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="9aa27-181">レポート先</span><span class="sxs-lookup"><span data-stu-id="9aa27-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="9aa27-182">レポート-uri</span><span class="sxs-lookup"><span data-stu-id="9aa27-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="9aa27-183">の `report-uri` 使用は推奨されなくなりましたが、 `report-to` すべての主要なブラウザーでがサポートされるまでは、両方のディレクティブを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9aa27-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="9aa27-184">を排他的に使用しない `report-uri` で `report-uri` ください。のサポートは、ブラウザーからいつ*でも*削除される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9aa27-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="9aa27-185">`report-uri`が完全にサポートされている場合は、ポリシー内ののサポートを削除 `report-to` します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="9aa27-186">の導入を追跡する方法に `report-to` ついては、「[レポートを使用](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)するには」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9aa27-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="9aa27-187">アプリのポリシーをリリースするたびにテストして更新します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9aa27-188">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="9aa27-188">Troubleshoot</span></span>

* <span data-ttu-id="9aa27-189">エラーは、ブラウザーの開発者ツールコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9aa27-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="9aa27-190">ブラウザーは次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="9aa27-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="9aa27-191">ポリシーに準拠していない要素。</span><span class="sxs-lookup"><span data-stu-id="9aa27-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="9aa27-192">ブロックされた項目を許可するようにポリシーを変更する方法。</span><span class="sxs-lookup"><span data-stu-id="9aa27-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="9aa27-193">ポリシーが完全に有効になるのは、クライアントのブラウザーが含まれているすべてのディレクティブをサポートしている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="9aa27-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="9aa27-194">現在のブラウザーのサポートマトリックスについては、「[使用可能: コンテンツ-セキュリティ-ポリシー](https://caniuse.com/#search=Content-Security-Policy)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9aa27-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa27-195">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9aa27-195">Additional resources</span></span>

* [<span data-ttu-id="9aa27-196">MDN web ドキュメント: コンテンツ-セキュリティ-ポリシー</span><span class="sxs-lookup"><span data-stu-id="9aa27-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="9aa27-197">コンテンツセキュリティポリシーレベル2</span><span class="sxs-lookup"><span data-stu-id="9aa27-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="9aa27-198">Google CSP エバリュエーター</span><span class="sxs-lookup"><span data-stu-id="9aa27-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
