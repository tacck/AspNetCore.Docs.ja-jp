<a name="csc"></a>

## <a name="combining-service-collection"></a>サービス コレクションの結合

いくつかのサービス コレクションを含む次の `ConfigureServices` について考えてみましょう。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

関連する登録グループは、サービスを登録するための拡張メソッドに移動できます。 たとえば、構成サービスは次のクラスに追加されます。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

残りのサービスは、同様のクラスに登録されます。 次の `ConfigureServices` では、新しい拡張メソッドを使用してサービスを登録します。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***注:*** 各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加、場合によっては構成します。 たとえば、<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> はビューが必要なサービス MVC コントローラーを追加します。 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> は Razor Pages が必要なサービスを追加します。 アプリをこの名前付け規則に従わせることをお勧めします。 拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。