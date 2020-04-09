## <a name="debug-diagnostics"></a>Diagnostica di debug

Per l'output diagnostico `Logging:LogLevel:Microsoft` `Debug`del routing dettagliato, impostare su . Ad esempio, nell'ambiente di sviluppo, impostare appsettings.For example, in the development environment, set *appsettings. Development.json*:

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}