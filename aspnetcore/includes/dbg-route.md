## <a name="debug-diagnostics"></a><span data-ttu-id="2fa08-101">Diagnostica di debug</span><span class="sxs-lookup"><span data-stu-id="2fa08-101">Debug diagnostics</span></span>

<span data-ttu-id="2fa08-102">Per un output di diagnostica dettagliato del routing, impostare `Logging:LogLevel:Microsoft` su `Debug` .</span><span class="sxs-lookup"><span data-stu-id="2fa08-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="2fa08-103">Nell'ambiente di sviluppo impostare il livello di registrazione in *appsettings.Development.js*in:</span><span class="sxs-lookup"><span data-stu-id="2fa08-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
