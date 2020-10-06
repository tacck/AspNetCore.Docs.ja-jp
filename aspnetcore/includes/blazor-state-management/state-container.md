入れ子になったコンポーネントは通常、「ASP.NET Core Blazor データ バインディング<xref:blazor/components/data-binding>」で説明されているように、"*チェーン バインド*" を使用してデータをバインドします。 入れ子になったコンポーネントと入れ子になっていないコンポーネントは、登録済みのメモリ内状態コンテナーを使用してデータへのアクセスを共有できます。 カスタムの状態コンテナー クラスでは、割り当て可能な <xref:System.Action> を使用して、状態変更のアプリのさまざまな部分でコンポーネントに通知できます。 次に例を示します。

* コンポーネントのペアでは、状態コンテナーを使用してプロパティを追跡します。
* この例のコンポーネントは入れ子になっていますが、この方法を使用する際に入れ子は必要ありません。

`StateContainer.cs`:

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

`Program.Main` (Blazor WebAssembly) では、次のようになります。

```csharp
builder.Services.AddSingleton<StateContainer>();
```

`Startup.ConfigureServices` (Blazor Server) では、次のようになります。

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Shared/Component2.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
