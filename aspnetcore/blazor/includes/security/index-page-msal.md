Index ページ (`wwwroot/index.html`) ページには、JavaScript で `AuthenticationService` を定義するスクリプトが含まれています。 `AuthenticationService` によって、OIDC プロトコルの下位レベルの詳細が処理されます。 アプリは、認証操作を実行するために、スクリプトで定義されているメソッドを内部的に呼び出します。

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
