<a name="csc"></a>

Si consideri il `ConfigureServices` metodo seguente, che registra i servizi e configura le opzioni:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

I gruppi di registrazioni correlati possono essere spostati in un metodo di estensione per registrare i servizi. I servizi di configurazione, ad esempio, vengono aggiunti alla classe seguente:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

I servizi rimanenti sono registrati in una classe simile. Il `ConfigureServices` metodo seguente usa i nuovi metodi di estensione per registrare i servizi:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Nota:_** Ogni `services.Add{GROUP_NAME}` metodo di estensione aggiunge e potenzialmente configura i servizi. Ad esempio, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> aggiunge i controller MVC dei servizi con le visualizzazioni require e <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> aggiunge i servizi richiesti Razor Pages. È consigliabile che le app seguano questa convenzione di denominazione. Inserire i metodi di estensione nello spazio dei nomi <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> per incapsulare i gruppi di registrazioni di servizio.
