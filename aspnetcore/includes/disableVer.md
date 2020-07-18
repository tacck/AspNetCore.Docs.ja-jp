<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>既定のアカウントの検証を無効にする

既定のテンプレートでは、ユーザーはにリダイレクトされ `Account.RegisterConfirmation` ます。ここで、アカウントを確認するためのリンクを選択できます。 既定値 `Account.RegisterConfirmation` はテストに***のみ***使用され、運用アプリでは自動アカウント検証を無効にする必要があります。

確認済みのアカウントを要求し、登録時にすぐにログインできないようにするには、 `DisplayConfirmAccountLink = false` */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*でを設定します。

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]