フレームワークは、既定ではポップアップ ログイン モードになり、ポップアップを開くことができない場合はリダイレクト ログイン モードに戻ります。 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> の `LoginMode` プロパティを `redirect` に設定して、リダイレクト ログイン モードを使用するように MSAL を構成します。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

既定の設定は `popup` であり、文字列の値の大文字と小文字は区別されません。
