## <a name="debug-diagnostics"></a>デバッグ診断

詳細なルーティング診断出力を行うには、`Logging:LogLevel:Microsoft` を `Debug` に設定してください。 開発環境では、*appsettings.Development.json* でログ レベルを次のように設定します。

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
