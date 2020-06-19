Blazor サーバー アプリはサーバー メモリに存在します。 これは、同じプロセス内で複数のアプリがホストされていることを意味します。 Blazor では各アプリ セッションに対して、独自の DI コンテナー スコープで回線が開始されます。 つまり、スコープが指定されたサービスは、Blazor セッションごとに一意になるということです。

> [!WARNING]
> 特別な注意を払っていない限り、同じサーバーの共有状態のアプリがシングルトン サービスを使用することはお勧めできません。これにより、回線をまたいだユーザー状態のリークなど、セキュリティ上の脆弱性が生じる可能性があります。

Blazor アプリでは、ステートフル シングルトン サービスを使用できます (特に専用に設計されている場合)。 たとえば、メモリ キャッシュをシングルトンとして使用するのは問題ありません。これは、使用されるキャッシュ キーをユーザーが制御できない場合に、特定のエントリにアクセスするためにキーが必要になるためです。

**また、セキュリティ上の理由から、Blazor アプリ内で <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を使用することはできません。** Blazor アプリは ASP.NET Core パイプラインのコンテキスト外で実行されるため、<xref:Microsoft.AspNetCore.Http.HttpContext> は <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 内で使用できるとは限りません。また、Blazor アプリを開始したコンテキストが保持されるとも保証されません。

Blazor アプリに要求状態を渡す方法としては、アプリの初期レンダリングでルート コンポーネントのパラメーターを使用することをお勧めします。

* Blazor アプリに渡すデータをすべて含むクラスを定義します。
* その時点で利用可能な <xref:Microsoft.AspNetCore.Http.HttpContext> を使用して、Razor ページからそのデータを設定します。
* ルート コンポーネント (アプリ) へのパラメーターとして Blazor アプリにデータを渡します。
* アプリに渡されるデータを保持するために、ルート コンポーネントでパラメーターを定義します。
* アプリ内のユーザー固有のデータを使用します。または、アプリ全体で使用できるようにそのデータを <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内のスコープ サービスにコピーします。

詳細およびプログラム例については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。
