## <a name="debug-diagnostics"></a>Diagnostica di debug

Per un output di diagnostica dettagliato del `Logging:LogLevel:Microsoft` routing `Debug`, impostare su. Nell'ambiente di sviluppo, ad esempio, impostare *appSettings. Development. JSON*:

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