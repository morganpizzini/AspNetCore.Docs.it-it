Nella tabella seguente sono specificati i parametri del generatore di codice ASP.NET Core:

| Parametro               | Descrizione|
| ----------------- | ------------ |
| -M  | Nome del modello. |
| -dc  | Contesto dati. |
| -udl | Uso del layout predefinito. |
| --relativeFolderPath | Percorso relativo della cartella di output per creare i file. |
| --useDefaultLayout | Per le viste deve essere usato il layout predefinito. |
| --referenceScriptLibraries | Aggiunge `_ValidationScriptsPartial` per modificare e creare pagine |

Utilizzare il commutatore `h` per ottenere assistenza sul comando `aspnet-codegenerator controller`:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Per altre informazioni, vedere [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)
