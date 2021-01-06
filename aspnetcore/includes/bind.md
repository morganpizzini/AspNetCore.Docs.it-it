Il modo migliore per leggere i valori di configurazione correlati consiste nell'usare il [modello di opzioni](xref:fundamentals/configuration/options). Ad esempio, per leggere i valori di configurazione seguenti:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Creare la `PositionOptions` classe seguente:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Classe Options:

* Deve essere non astratto con un costruttore pubblico senza parametri.
* Tutte le proprietà di lettura/scrittura pubbliche del tipo sono associate.
* I campi sono ***not** _ Bound. Nel codice precedente `Position` non è associato. La `Position` proprietà viene usata in modo che la stringa `"Position"` non debba essere hardcoded nell'app quando si associa la classe a un provider di configurazione.

Il codice seguente:

_ Chiama [ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) per associare la `PositionOptions` classe alla `Position` sezione.
* Consente di visualizzare i `Position` dati di configurazione.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

Nel codice precedente, per impostazione predefinita, vengono lette le modifiche apportate al file di configurazione JSON dopo l'avvio dell'app.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) associa e restituisce il tipo specificato. `ConfigurationBinder.Get<T>` può essere più pratico rispetto all'uso di `ConfigurationBinder.Bind` . Il codice seguente illustra come usare `ConfigurationBinder.Get<T>` con la `PositionOptions` classe:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Nel codice precedente, per impostazione predefinita, vengono lette le modifiche apportate al file di configurazione JSON dopo l'avvio dell'app.

Un approccio alternativo quando si usa il **modello * Options** _ consiste nel associare la `Position` sezione e aggiungerla al [contenitore del servizio di inserimento delle dipendenze](xref:fundamentals/dependency-injection). Nel codice seguente, `PositionOptions` viene aggiunto al contenitore del servizio con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> e associato alla configurazione:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Utilizzando il codice precedente, il codice seguente legge le opzioni di posizione:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Nel codice precedente, le modifiche apportate al file di configurazione JSON dopo l'avvio dell'app ***non*** vengono lette. Per leggere le modifiche dopo l'avvio dell'app, usare [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
