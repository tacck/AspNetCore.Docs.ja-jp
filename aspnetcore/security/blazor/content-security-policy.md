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
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)は、攻撃者が1つまたは複数の悪意のあるクライアント側スクリプトをアプリのレンダリングされたコンテンツに配置するセキュリティの脆弱性です。 コンテンツセキュリティポリシー (CSP) は、ブラウザーに有効なことを通知することで、XSS 攻撃から保護するのに役立ちます。

* スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。
* ページによって実行されるアクション。フォームの許可された URL ターゲットを指定します。
* 読み込むことができるプラグイン。

CSP をアプリに適用するために、開発者は1つ以上*directives*の`Content-Security-Policy`ヘッダーまた`<meta>`はタグに csp コンテンツのセキュリティディレクティブをいくつか指定します。

ポリシーは、ページの読み込み中にブラウザーによって評価されます。 ブラウザーはページのソースを検査し、コンテンツセキュリティディレクティブの要件を満たしているかどうかを判断します。 リソースのポリシーディレクティブが満たされていない場合、ブラウザーはリソースを読み込みません。 たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。 属性にサードパーティの`<script>`元のタグが含まれている場合は、ブラウザーによってスクリプトの読み込みが禁止されます。 `src`

CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイルブラウザーでサポートされています。 アプリには CSP Blazorを使用することをお勧めします。

## <a name="policy-directives"></a>ポリシーディレクティブ

最小で、次のアプリのBlazorディレクティブとソースを指定します。 必要に応じて、ディレクティブとソースを追加します。 この記事の「[ポリシーの適用](#apply-the-policy)」セクションでは、次のディレクティブを使用していBlazorます。ここではBlazor 、webassembly サーバーのセキュリティポリシーの例を示しています。

* [base uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash;は、ページの`<base>`タグの url を制限します。 を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [すべてのブロック-混合コンテンツ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash;は、混合 HTTP と HTTPS コンテンツを読み込むことができません。
* [既定値-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash;は、ポリシーによって明示的に指定されていないソースディレクティブのフォールバックを示します。 を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash;は、イメージの有効なソースを示します。
  * Url `data:`からのイメージの`data:`読み込みを許可するには、を指定します。
  * HTTPS `https:`エンドポイントからのイメージの読み込みを許可するには、を指定します。
* [オブジェクト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash;は`<object>`、、 `<embed>`、および`<applet>`タグの有効なソースを示します。 すべて`none`の URL ソースを禁止するには、を指定します。
* [スクリプト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash;は、スクリプトの有効なソースを示します。
  * ブートストラップスクリプト`https://stackpath.bootstrapcdn.com/`のホストソースを指定します。
  * を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * Webassembly の場合Blazor :
    * 読み込む必要Blazorがあるインラインスクリプトを許可するには、次のハッシュを指定します。
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 文字列`unsafe-eval`からコード`eval()`を作成するためにメソッドとメソッドを使用するように指定します。
  * Blazorサーバーアプリで、スタイルシートの`sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=`フォールバック検出を実行するインラインスクリプトのハッシュを指定します。
* [スタイル-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash;は、スタイルシートの有効なソースを示します。
  * ブートストラップスタイル`https://stackpath.bootstrapcdn.com/`シートのホストソースを指定します。
  * を`self`指定すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * インライン`unsafe-inline`スタイルの使用を許可するには、を指定します。 初期要求後にクライアントとサーバーを再Blazor接続するために、サーバーアプリの UI でインライン宣言が必要です。 今後のリリースで`unsafe-inline`は、が不要になるように、インラインスタイルが削除される可能性があります。
* [アップグレード-安全で](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash;はない要求は、セキュリティで保護されていない (HTTP) ソースからのコンテンツ url を HTTPS 経由で安全に取得する必要があることを示します。

上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。

追加のインラインスクリプトの SHA ハッシュを取得するには、次のようにします。

* 「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。
* アプリをローカルで実行しながら、ブラウザーの開発者ツールコンソールにアクセスします。 CSP ヘッダーまたは`meta`タグが存在する場合、ブラウザーはブロックされたスクリプトのハッシュを計算して表示します。
* ブラウザーによって提供されるハッシュを`script-src`ソースにコピーします。 各ハッシュを囲む単一引用符を使用します。

コンテンツセキュリティポリシーレベル2のブラウザーサポートマトリックスについては、「[使用可能: コンテンツセキュリティポリシーレベル 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」を参照してください。

## <a name="apply-the-policy"></a>ポリシーの適用

ポリシーを`<meta>`適用するには、タグを使用します。

* `http-equiv`属性の値をに`Content-Security-Policy`設定します。
* `content`属性値にディレクティブを配置します。 ディレクティブはセミコロン (`;`) で区切ります。
* 常にタグ`meta`を`<head>`コンテンツに配置します。

以下のセクションでは、webassembly Blazor Blazorサーバーのポリシーの例を示します。 これらの例は、のBlazor各リリースについて、この記事でバージョン管理されています。 リリースに適したバージョンを使用するには、この web ページの**バージョン**ドロップダウンセレクターでドキュメントバージョンを選択します。

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Wwwroot/index.html ホスト`<head>`ページのコンテンツ*wwwroot/index.html*で、「[ポリシーディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。

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

### <a name="blazor-server"></a>Blazor サーバー

`<head>` *Pages/_Host*の [ホスト] ページの内容で、[[ポリシーディレクティブ](#policy-directives)] セクションで説明されているディレクティブを適用します。

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

## <a name="meta-tag-limitations"></a>Meta タグの制限事項

タグ`<meta>`ポリシーは、次のディレクティブをサポートしていません。

* [フレーム-先祖](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [サンドボックス](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

上記のディレクティブをサポートするには、と`Content-Security-Policy`いう名前のヘッダーを使用します。 ディレクティブ文字列は、ヘッダーの値です。

## <a name="test-a-policy-and-receive-violation-reports"></a>ポリシーをテストして違反レポートを受信する

テストは、初期ポリシーを作成するときに、サードパーティ製のスクリプトが誤ってブロックされていないことを確認するのに役立ちます。

ポリシーディレクティブを適用せずに一定期間にわたってポリシーをテストするに`<meta>`は、 `http-equiv`ヘッダーベースのポリシーのタグの属性またはヘッダー `Content-Security-Policy-Report-Only`名をに設定します。 エラーレポートは、JSON ドキュメントとして指定された URL に送信されます。 詳細については、 [MDN web ドキュメント: コンテンツ-セキュリティポリシー-レポートのみ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)を参照してください。

ポリシーがアクティブになっているときの違反を報告するには、次の記事を参照してください。

* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

の`report-uri`使用は推奨されなくなりましたが、すべての`report-to`主要なブラウザーでがサポートされるまでは、両方のディレクティブを使用する必要があります。 を排他的`report-uri`に使用し`report-uri`ないでください。のサポートは、ブラウザーからいつ*でも*削除される可能性があります。 が完全に`report-uri`サポートされて`report-to`いる場合は、ポリシー内ののサポートを削除します。 の`report-to`導入を追跡する方法については、「[レポートを使用](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)するには」を参照してください。

アプリのポリシーをリリースするたびにテストして更新します。

## <a name="troubleshoot"></a>トラブルシューティング

* エラーは、ブラウザーの開発者ツールコンソールに表示されます。 ブラウザーは次の情報を提供します。
  * ポリシーに準拠していない要素。
  * ブロックされた項目を許可するようにポリシーを変更する方法。
* ポリシーが完全に有効になるのは、クライアントのブラウザーが含まれているすべてのディレクティブをサポートしている場合のみです。 現在のブラウザーのサポートマトリックスについては、「[使用可能: コンテンツ-セキュリティ-ポリシー](https://caniuse.com/#search=Content-Security-Policy)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [MDN web ドキュメント: コンテンツ-セキュリティ-ポリシー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [コンテンツセキュリティポリシーレベル2](https://www.w3.org/TR/CSP2/)
* [Google CSP エバリュエーター](https://csp-evaluator.withgoogle.com/)
