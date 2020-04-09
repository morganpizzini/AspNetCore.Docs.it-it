> [!WARNING]
> Per motivi di sicurezza, è necessario acconsentire esplicitamente all'associazione dei dati della richiesta `GET` alle proprietà del modello di pagina. Verificare l'input dell'utente prima di eseguirne il mapping alle proprietà. L'attivazione nell'associazione `GET` è utile quando si indirizzano scenari che si basano su valori di stringa di query o route.
>
> Per associare `GET` una proprietà [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) alle richieste, impostare la proprietà dell'attributo `SupportsGet` su `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Per ulteriori informazioni, consultate [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
