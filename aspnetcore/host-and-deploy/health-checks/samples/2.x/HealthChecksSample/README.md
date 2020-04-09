# <a name="aspnet-core-health-check-sample"></a>Esempio di controllo integrità di ASP.NET Core

In questo esempio viene illustrato l'uso del middleware e dei servizi del controllo integrità. Questo esempio è una dimostrazione dello scenario descritto nell'argomento [Health checks in ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) (Controlli integrità in ASP.NET Core).

Per eseguire l'app di esempio per uno scenario descritto nell'argomento, usare il comando [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) dalla cartella del progetto in una shell dei comandi. Inserire un'opzione per lo scenario che si sta esplorando. Per impostazione predefinita, la configurazione dell'app diventa `basic` se non si specifica un'opzione in `dotnet run`.

| Scenario                                               | Comando app di esempio               | Descrizione |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Probe di integrità di base (valore predefinito)                           | `dotnet run --scenario basic`    | Conferma che l'app può elaborare le richieste HTTP. |
| Probe del database                                         | `dotnet run --scenario db`       | Controlla una connessione al database SQL Server. Per istruzioni, vedere la sezione [Database probe](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) (Probe del database) dell'argomento. |
| Probe di idoneità/attività                              | `dotnet run --scenario liveness` | Esegue i controlli dello stato dell'app attiva (*attività*) o della preparazione dell'app a diventare attiva (*idoneità*). |
| Probe basato su metrica (memoria)/<br>writer di risposta personalizzata | `dotnet run --scenario writer`   | Esegue controlli sull'utilizzo della memoria e scrive codice JSON personalizzato quando viene controllato l'endpoint di integrità. |
| Filtrare in base alla porta                                         | `dotnet run --scenario port`     | Filtra controlli di integrità per una determinata porta. Per istruzioni, vedere la sezione [Filter by port](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) (Filtrare per porta) dell'argomento. |

Gli scenari relativi al probe del database e al filtro per porta richiedono una configurazione aggiuntiva. Per informazioni dettagliate, vedere l'argomento [Health checks](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) (Controlli integrità).
