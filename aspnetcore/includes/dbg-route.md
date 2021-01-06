## <a name="debug-diagnostics"></a>Diagnostica di debug

Per un output di diagnostica dettagliato del routing, impostare `Logging:LogLevel:Microsoft` su `Debug` . Nell'ambiente di sviluppo impostare il livello di registrazione in *appsettings.Development.js* in:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
