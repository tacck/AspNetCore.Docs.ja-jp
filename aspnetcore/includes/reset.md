<span data-ttu-id="3dd1b-101">リセットを使用すると、指定されたエラー コードを使用してサーバーに HTTP/2 要求をリセットさせることができます。</span><span class="sxs-lookup"><span data-stu-id="3dd1b-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="3dd1b-102">リセット要求は中止されたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="3dd1b-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="3dd1b-103">上記のコード例の `Reset` により、`INTERNAL_ERROR` エラー コードが指定されています。</span><span class="sxs-lookup"><span data-stu-id="3dd1b-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="3dd1b-104">HTTP/2 エラー コードの詳細については、[HTTP/2 仕様の「エラーコード」セクション](https://tools.ietf.org/html/rfc7540#page-50)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3dd1b-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>