## <a name="debug-diagnostics"></a><span data-ttu-id="deff7-101">Diagnostica di debug</span><span class="sxs-lookup"><span data-stu-id="deff7-101">Debug diagnostics</span></span>

<span data-ttu-id="deff7-102">Per l'output diagnostico `Logging:LogLevel:Microsoft` `Debug`del routing dettagliato, impostare su .</span><span class="sxs-lookup"><span data-stu-id="deff7-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="deff7-103">Ad esempio, nell'ambiente di sviluppo, impostare appsettings.For example, in the development environment, set *appsettings. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="deff7-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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