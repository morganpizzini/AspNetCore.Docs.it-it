<a name="codegenerator"></a> Nella tabella seguente sono specificati i parametri del generatore di codice ASP.NET Core:

| Parametro               | Descrizione|
| ----------------- | ------------ |
| -M  | Il nome del modello. |
| -dc  | Classe `DbContext` da usare. |
| -udl | Uso del layout predefinito. |
| -outDir | Percorso relativo della cartella di output per creare le viste. |
| --referenceScriptLibraries | Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine |

Utilizzare il commutatore `h` per ottenere assistenza sul comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Per ulteriori informazioni, vedere [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
