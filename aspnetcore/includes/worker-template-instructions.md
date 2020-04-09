# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.
1. Selezionare **Servizio di lavoro**. Selezionare **Avanti**.
1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Selezionare **Create** (Crea).
1. Nella finestra di dialogo **Crea un nuovo servizio di lavoro** selezionare **Crea**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Creare un nuovo progetto.
1. Seleziona **App** in **.NET Core** nella barra laterale.
1. Selezionare **Lavoratore** in **ASP.NET Core**. Selezionare **Avanti**.
1. Selezionare **.NET Core 3.0** o versione successiva per Framework di **destinazione**. Selezionare **Avanti**.
1. Specificare un nome nel campo **Nome progetto.** Selezionare **Create** (Crea).

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare il servizio di ruolo di lavoro (`worker`) con il comando[dotnet new](/dotnet/core/tools/dotnet-new) da una shell dei comandi. Nell'esempio seguente viene creata un'app del servizio di ruolo di lavoro denominata `ContosoWorker`. Una cartella per l'app `ContosoWorker` viene creata automaticamente quando viene eseguito il comando.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
