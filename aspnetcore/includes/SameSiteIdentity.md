ASP.NET Core [id](xref:security/authentication/identity)は、や統合などの高度なシナリオを除き、 [SameSite cookie](xref:security/samesite)の主な影響を受け `IFrames` `OpenIdConnect` ません。

を使用する場合 `Identity` は、クッキープロバイダーを追加したり、を呼び出したり***しないで*** ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` ください。