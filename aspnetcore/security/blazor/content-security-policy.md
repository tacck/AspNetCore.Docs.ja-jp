---
title: ' ASP.NET Core にコンテンツセキュリティポリシーを適用し Blazor ます。説明: ' ASP.NET Core アプリでコンテンツセキュリティポリシー (CSP) を使用し Blazor てクロスサイトスクリプティング (XSS) 攻撃から保護する方法について説明します。 '
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>ASP.NET Core のコンテンツセキュリティポリシーを適用するBlazor

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)は、攻撃者が1つまたは複数の悪意のあるクライアント側スクリプトをアプリのレンダリングされたコンテンツに配置するセキュリティの脆弱性です。 コンテンツセキュリティポリシー (CSP) は、ブラウザーに有効なことを通知することで、XSS 攻撃から保護するのに役立ちます。

* スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。
* ページによって実行されるアクション。フォームの許可された URL ターゲットを指定します。
* 読み込むことができるプラグイン。

CSP をアプリに適用するために、開発者は1つ以上のヘッダーまたはタグに CSP コンテンツのセキュリティ*ディレクティブ*をいくつか指定し `Content-Security-Policy` `<meta>` ます。

ポリシーは、ページの読み込み中にブラウザーによって評価されます。 ブラウザーはページのソースを検査し、コンテンツセキュリティディレクティブの要件を満たしているかどうかを判断します。 リソースのポリシーディレクティブが満たされていない場合、ブラウザーはリソースを読み込みません。 たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。 属性にサードパーティの元のタグが含まれている場合は、 `<script>` `src` ブラウザーによってスクリプトの読み込みが禁止されます。

CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイルブラウザーでサポートされています。 アプリには CSP を使用することをお勧め Blazor します。

## <a name="policy-directives"></a>ポリシーディレクティブ

最小で、次のアプリのディレクティブとソースを指定し Blazor ます。 必要に応じて、ディレクティブとソースを追加します。 この記事の「[ポリシーの適用](#apply-the-policy)」セクションでは、次のディレクティブを使用しています。ここでは、webassembly サーバーのセキュリティポリシーの例を示し Blazor Blazor ています。

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ページのタグの url を制限 `<base>` します。 を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): 混合 HTTP と HTTPS コンテンツの読み込みを禁止します。
* [既定値-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): ポリシーで明示的に指定されていないソースディレクティブのフォールバックを示します。 を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): イメージの有効なソースを示します。
  * `data:`Url からのイメージの読み込みを許可するには、を指定し `data:` ます。
  * `https:`HTTPS エンドポイントからのイメージの読み込みを許可するには、を指定します。
* [オブジェクト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>` 、、およびタグの有効なソースを示し `<embed>` `<applet>` ます。 `none`すべての URL ソースを禁止するには、を指定します。
* [スクリプト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): スクリプトの有効なソースを示します。
  * `https://stackpath.bootstrapcdn.com/`ブートストラップスクリプトのホストソースを指定します。
  * を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * BlazorWebassembly の場合:
    * 読み込む必要があるインラインスクリプトを許可するには、次のハッシュを指定し Blazor ます。
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * `unsafe-eval`文字列からコードを作成するためにメソッドとメソッドを使用するように指定し `eval()` ます。
  * Blazorサーバーアプリで、 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` スタイルシートのフォールバック検出を実行するインラインスクリプトのハッシュを指定します。
* [スタイル-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): スタイルシートの有効なソースを示します。
  * `https://stackpath.bootstrapcdn.com/`ブートストラップスタイルシートのホストソースを指定します。
  * を指定 `self` すると、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * `unsafe-inline`インラインスタイルの使用を許可するには、を指定します。 Blazor初期要求後にクライアントとサーバーを再接続するために、サーバーアプリの UI でインライン宣言が必要です。 今後のリリースでは、が不要になるように、インラインスタイルが削除される可能性があり `unsafe-inline` ます。
* [upgrade-セキュリティで](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)保護されていない要求: セキュリティで保護されていない (HTTP) ソースからのコンテンツ URL を HTTPS 経由で安全に取得する必要があることを示します。

上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。

追加のインラインスクリプトの SHA ハッシュを取得するには、次のようにします。

* 「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。
* アプリをローカルで実行しながら、ブラウザーの開発者ツールコンソールにアクセスします。 CSP ヘッダーまたはタグが存在する場合、ブラウザーはブロックされたスクリプトのハッシュを計算して表示し `meta` ます。
* ブラウザーによって提供されるハッシュをソースにコピーし `script-src` ます。 各ハッシュを囲む単一引用符を使用します。

コンテンツセキュリティポリシーレベル2のブラウザーサポートマトリックスについては、「[使用可能: コンテンツセキュリティポリシーレベル 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」を参照してください。

## <a name="apply-the-policy"></a>ポリシーの適用

ポリシーを `<meta>` 適用するには、タグを使用します。

* 属性の値 `http-equiv` をに設定 `Content-Security-Policy` します。
* 属性値にディレクティブを配置 `content` します。 ディレクティブはセミコロン () で区切り `;` ます。
* 常に `meta` タグをコンテンツに配置 `<head>` します。

以下のセクションでは、webassembly サーバーのポリシーの例を示し Blazor Blazor ます。 これらの例は、の各リリースについて、この記事でバージョン管理されてい Blazor ます。 リリースに適したバージョンを使用するには、この web ページの**バージョン**ドロップダウンセレクターでドキュメントバージョンを選択します。

### <a name="blazor-webassembly"></a>Blazor WebAssembly

`<head>` *Wwwroot/index.html*ホストページのコンテンツで、「[ポリシーディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。

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

`<meta>`タグポリシーは、次のディレクティブをサポートしていません。

* [フレーム-先祖](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [サンドボックス](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

上記のディレクティブをサポートするには、という名前のヘッダーを使用し `Content-Security-Policy` ます。 ディレクティブ文字列は、ヘッダーの値です。

## <a name="test-a-policy-and-receive-violation-reports"></a>ポリシーをテストして違反レポートを受信する

テストは、初期ポリシーを作成するときに、サードパーティ製のスクリプトが誤ってブロックされていないことを確認するのに役立ちます。

ポリシーディレクティブを適用せずに一定期間にわたってポリシーをテストするには、 `<meta>` `http-equiv` ヘッダーベースのポリシーのタグの属性またはヘッダー名をに設定し `Content-Security-Policy-Report-Only` ます。 エラーレポートは、JSON ドキュメントとして指定された URL に送信されます。 詳細については、 [MDN web ドキュメント: コンテンツ-セキュリティポリシー-レポートのみ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)を参照してください。

ポリシーがアクティブになっているときの違反を報告するには、次の記事を参照してください。

* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

の `report-uri` 使用は推奨されなくなりましたが、 `report-to` すべての主要なブラウザーでがサポートされるまでは、両方のディレクティブを使用する必要があります。 を排他的に使用しない `report-uri` で `report-uri` ください。のサポートは、ブラウザーからいつ*でも*削除される可能性があります。 `report-uri`が完全にサポートされている場合は、ポリシー内ののサポートを削除 `report-to` します。 の導入を追跡する方法に `report-to` ついては、「[レポートを使用](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)するには」を参照してください。

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
