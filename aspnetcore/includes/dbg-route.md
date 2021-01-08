## <a name="debug-diagnostics"></a><span data-ttu-id="9f40a-101">デバッグ診断</span><span class="sxs-lookup"><span data-stu-id="9f40a-101">Debug diagnostics</span></span>

<span data-ttu-id="9f40a-102">詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。</span><span class="sxs-lookup"><span data-stu-id="9f40a-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="9f40a-103">開発環境では、*appsettings.Development.json* でログ レベルを次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="9f40a-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
