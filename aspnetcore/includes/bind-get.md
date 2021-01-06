> [!WARNING]
> <span data-ttu-id="fd0da-101">Per motivi di sicurezza, è necessario acconsentire esplicitamente all'associazione dei dati della richiesta `GET` alle proprietà del modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="fd0da-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="fd0da-102">Verificare l'input dell'utente prima di eseguirne il mapping alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="fd0da-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="fd0da-103">La scelta dell' `GET` associazione è utile per gli scenari che si basano sulla stringa di query o sui valori della route.</span><span class="sxs-lookup"><span data-stu-id="fd0da-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="fd0da-104">Per associare una proprietà alle `GET` richieste, impostare la [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) proprietà dell'attributo su `SupportsGet` `true` :</span><span class="sxs-lookup"><span data-stu-id="fd0da-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="fd0da-105">Per altre informazioni, vedere [ASP.NET Core community standup: bind on Get Discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="fd0da-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
