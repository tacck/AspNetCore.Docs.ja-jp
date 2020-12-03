<span data-ttu-id="bc75b-101">フレームワークは、既定ではポップアップ ログイン モードになり、ポップアップを開くことができない場合はリダイレクト ログイン モードに戻ります。</span><span class="sxs-lookup"><span data-stu-id="bc75b-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="bc75b-102"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の `LoginMode` プロパティを `redirect` に設定して、リダイレクト ログイン モードを使用するように MSAL を構成します。</span><span class="sxs-lookup"><span data-stu-id="bc75b-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="bc75b-103">既定の設定は `popup` であり、文字列の値の大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="bc75b-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
