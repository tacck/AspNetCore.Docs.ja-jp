リセットを使用すると、指定されたエラー コードを使用してサーバーに HTTP/2 要求をリセットさせることができます。 リセット要求は中止されたと見なされます。

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

上記のコード例の `Reset` により、`INTERNAL_ERROR` エラー コードが指定されています。 HTTP/2 エラー コードの詳細については、[HTTP/2 仕様の「エラーコード」セクション](https://tools.ietf.org/html/rfc7540#page-50)を参照してください。