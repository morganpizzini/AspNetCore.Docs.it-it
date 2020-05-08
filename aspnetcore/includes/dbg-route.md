## <a name="debug-diagnostics"></a><span data-ttu-id="aa79a-101">Diagnostica di debug</span><span class="sxs-lookup"><span data-stu-id="aa79a-101">Debug diagnostics</span></span>

<span data-ttu-id="aa79a-102">Per un output di diagnostica dettagliato del `Logging:LogLevel:Microsoft` routing `Debug`, impostare su.</span><span class="sxs-lookup"><span data-stu-id="aa79a-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="aa79a-103">Nell'ambiente di sviluppo, ad esempio, impostare *appSettings. Development. JSON*:</span><span class="sxs-lookup"><span data-stu-id="aa79a-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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