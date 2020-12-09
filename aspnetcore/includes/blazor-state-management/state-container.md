<span data-ttu-id="e494e-101">I componenti annidati in genere associano i dati mediante *Binding concatenato* come descritto in <xref:blazor/components/data-binding> .</span><span class="sxs-lookup"><span data-stu-id="e494e-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="e494e-102">I componenti annidati e non annidati possono condividere l'accesso ai dati usando un contenitore di stato in memoria registrato.</span><span class="sxs-lookup"><span data-stu-id="e494e-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="e494e-103">Una classe di contenitori di stato personalizzata può usare un'assegnabile <xref:System.Action> per notificare ai componenti in parti diverse dell'app le modifiche di stato.</span><span class="sxs-lookup"><span data-stu-id="e494e-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="e494e-104">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e494e-104">In the following example:</span></span>

* <span data-ttu-id="e494e-105">Una coppia di componenti usa un contenitore di stato per tenere traccia di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="e494e-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="e494e-106">I componenti dell'esempio sono annidati, ma l'annidamento non è necessario per il funzionamento di questo approccio.</span><span class="sxs-lookup"><span data-stu-id="e494e-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="e494e-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="e494e-107">`StateContainer.cs`:</span></span>

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

<span data-ttu-id="e494e-108">In `Program.Main` (webassembly Blazer):</span><span class="sxs-lookup"><span data-stu-id="e494e-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="e494e-109">In `Startup.ConfigureServices` (server Blazer):</span><span class="sxs-lookup"><span data-stu-id="e494e-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="e494e-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="e494e-110">`Pages/Component1.razor`:</span></span>

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

<span data-ttu-id="e494e-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="e494e-111">`Shared/Component2.razor`:</span></span>

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
