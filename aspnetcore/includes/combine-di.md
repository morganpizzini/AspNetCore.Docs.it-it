<a name="csc"></a>

## <a name="combining-service-collection"></a>Combinazione della raccolta di servizi

Tenere presente quanto segue `ConfigureServices` che contiene diverse raccolte di servizi:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

I gruppi di registrazioni correlati possono essere spostati in un metodo di estensione per registrare i servizi. I servizi di configurazione, ad esempio, vengono aggiunti alla classe seguente:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

I servizi rimanenti sono registrati in una classe simile. Di seguito `ConfigureServices` vengono utilizzati i nuovi metodi di estensione per registrare i servizi:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Nota:*** Ogni `services.Add{SERVICE_NAME}` metodo di estensione aggiunge e potenzialmente configura i servizi. Ad esempio, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> aggiunge i controller MVC dei servizi con le visualizzazioni require. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>aggiunge i servizi Razor Pages necessario. È consigliabile che le app seguano questa convenzione di denominazione. Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.