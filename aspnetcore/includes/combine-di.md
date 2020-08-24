<a name="csc"></a>

サービスを登録し、オプションを構成する次の `ConfigureServices` メソッドについて考えてみましょう。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

関連する登録グループは、サービスを登録するための拡張メソッドに移動できます。 たとえば、構成サービスは次のクラスに追加されます。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

残りのサービスは、同様のクラスに登録されます。 次の `ConfigureServices` メソッドでは、新しい拡張メソッドを使用してサービスを登録します。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_注:_** 各 `services.Add{GROUP_NAME}` 拡張メソッドは、サービスを追加、場合によっては構成します。 たとえば、<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ではビューを持つ MVC コントローラーで必要なサービスが追加され、<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> では Razor Pages で必要なサービスが追加されます。 アプリをこの名前付け規則に従わせることをお勧めします。 拡張メソッドを <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 名前空間に配置して、サービス登録のグループをカプセル化します。
