<span data-ttu-id="950bd-101">Index ページ (`wwwroot/index.html`) ページには、JavaScript で `AuthenticationService` を定義するスクリプトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="950bd-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="950bd-102">`AuthenticationService` によって、OIDC プロトコルの下位レベルの詳細が処理されます。</span><span class="sxs-lookup"><span data-stu-id="950bd-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="950bd-103">アプリは、認証操作を実行するために、スクリプトで定義されているメソッドを内部的に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="950bd-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
