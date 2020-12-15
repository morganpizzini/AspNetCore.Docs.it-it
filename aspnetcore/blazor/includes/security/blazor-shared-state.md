Le app del server Blazer risiedono nella memoria del server. Ciò significa che sono presenti più app ospitate all'interno dello stesso processo. Per ogni sessione dell'app, blazer avvia un circuito con il proprio ambito del contenitore DI. Ciò significa che i servizi con ambito sono univoci per ogni sessione di Blazer.

> [!WARNING]
> Non sono consigliate le app nello stesso stato di condivisione server usando i servizi singleton, a meno che non venga presa una particolare attenzione, in quanto questo può introdurre vulnerabilità di sicurezza, ad esempio la perdita dello stato utente tra i circuiti.

È possibile usare i servizi singleton con stato nelle app Blazer se sono appositamente progettati. Ad esempio, è possibile usare una cache in memoria come singleton perché richiede una chiave per accedere a una determinata voce, supponendo che gli utenti non abbiano il controllo delle chiavi di cache usate.

**Inoltre, per motivi di sicurezza, non è necessario usare <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> nelle app blazer.** Le app Blazer vengono eseguite al di fuori del contesto della pipeline ASP.NET Core. Non è garantito che sia <xref:Microsoft.AspNetCore.Http.HttpContext> disponibile all'interno di <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , né che contenga il contesto che ha avviato l'app blazer.

Il modo consigliato per passare lo stato della richiesta all'app Blaze consiste nell'usare i parametri per il componente radice nel rendering iniziale dell'app:

* Definire una classe con tutti i dati che si desidera passare all'app blazer.
* Popolare i dati dalla pagina Razor usando la <xref:Microsoft.AspNetCore.Http.HttpContext> disponibile in quel momento.
* Passare i dati all'app blazer come parametro al componente radice (app).
* Definire un parametro nel componente radice per conservare i dati passati all'app.
* Usare i dati specifici dell'utente all'interno dell'app; in alternativa, copiare i dati in un servizio con ambito in in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> modo che possa essere usato nell'app.

Per ulteriori informazioni e un codice di esempio, vedere <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
