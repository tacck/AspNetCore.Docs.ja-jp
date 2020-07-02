## <a name="debug-diagnostics"></a><span data-ttu-id="a898b-101">デバッグ診断</span><span class="sxs-lookup"><span data-stu-id="a898b-101">Debug diagnostics</span></span>

<span data-ttu-id="a898b-102">詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。</span><span class="sxs-lookup"><span data-stu-id="a898b-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="a898b-103">開発環境で、appsettings.Development.jsのログレベルを次*の*ように設定します。</span><span class="sxs-lookup"><span data-stu-id="a898b-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
