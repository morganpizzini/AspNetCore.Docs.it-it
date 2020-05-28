---
<span data-ttu-id="44d4b-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="44d4b-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="44d4b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="44d4b-102">'Blazor'</span></span>
- <span data-ttu-id="44d4b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="44d4b-103">'Identity'</span></span>
- <span data-ttu-id="44d4b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="44d4b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="44d4b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="44d4b-105">'Razor'</span></span>
- <span data-ttu-id="44d4b-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="44d4b-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="44d4b-107">Configurare l'autenticazione del certificato in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44d4b-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="44d4b-108">`Microsoft.AspNetCore.Authentication.Certificate`contiene un'implementazione simile all' [autenticazione del certificato](https://tools.ietf.org/html/rfc5246#section-7.4.4) per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44d4b-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="44d4b-109">L'autenticazione del certificato viene eseguita a livello di TLS, molto prima che venga mai ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44d4b-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="44d4b-110">Più precisamente, si tratta di un gestore di autenticazione che convalida il certificato e quindi fornisce un evento in cui è possibile risolvere il certificato in un `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="44d4b-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="44d4b-111">[Configurare l'host](#configure-your-host-to-require-certificates) per l'autenticazione del certificato, essere IIS, gheppio, app Web di Azure o qualsiasi altro elemento in uso.</span><span class="sxs-lookup"><span data-stu-id="44d4b-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="44d4b-112">Scenari di proxy e bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="44d4b-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="44d4b-113">L'autenticazione del certificato è uno scenario con stato usato principalmente quando un proxy o un servizio di bilanciamento del carico non gestisce il traffico tra client e server.</span><span class="sxs-lookup"><span data-stu-id="44d4b-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="44d4b-114">Se si usa un proxy o un servizio di bilanciamento del carico, l'autenticazione del certificato funziona solo se il proxy o il servizio di bilanciamento del carico:</span><span class="sxs-lookup"><span data-stu-id="44d4b-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="44d4b-115">Gestisce l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-115">Handles the authentication.</span></span>
* <span data-ttu-id="44d4b-116">Passa le informazioni di autenticazione utente all'app, ad esempio in un'intestazione di richiesta, che agisce sulle informazioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="44d4b-117">Un'alternativa all'autenticazione del certificato negli ambienti in cui vengono usati proxy e servizi di bilanciamento del carico è Active Directory servizi federati (ADFS) con OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="44d4b-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="44d4b-118">Introduzione</span><span class="sxs-lookup"><span data-stu-id="44d4b-118">Get started</span></span>

<span data-ttu-id="44d4b-119">Acquisire un certificato HTTPS, applicarlo e [configurare l'host](#configure-your-host-to-require-certificates) per richiedere i certificati.</span><span class="sxs-lookup"><span data-stu-id="44d4b-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="44d4b-120">Nell'app Web aggiungere un riferimento al `Microsoft.AspNetCore.Authentication.Certificate` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="44d4b-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="44d4b-121">Quindi, nel `Startup.ConfigureServices` metodo chiamare `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` con le opzioni, fornendo un delegato per `OnCertificateValidated` per eseguire qualsiasi convalida supplementare sul certificato client inviato con le richieste.</span><span class="sxs-lookup"><span data-stu-id="44d4b-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="44d4b-122">Trasformare le informazioni in un oggetto `ClaimsPrincipal` e impostarle sulla `context.Principal` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="44d4b-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="44d4b-123">Se l'autenticazione ha esito negativo, questo gestore restituisce una `403 (Forbidden)` risposta anziché un `401 (Unauthorized)` , come si può immaginare.</span><span class="sxs-lookup"><span data-stu-id="44d4b-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="44d4b-124">Il motivo è che l'autenticazione deve verificarsi durante la connessione TLS iniziale.</span><span class="sxs-lookup"><span data-stu-id="44d4b-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="44d4b-125">Quando raggiunge il gestore, è troppo tardi.</span><span class="sxs-lookup"><span data-stu-id="44d4b-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="44d4b-126">Non è possibile aggiornare la connessione da una connessione anonima a un'altra con un certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="44d4b-127">Aggiungere anche `app.UseAuthentication();` il `Startup.Configure` metodo.</span><span class="sxs-lookup"><span data-stu-id="44d4b-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="44d4b-128">In caso contrario, `HttpContext.User` non verrà impostato su `ClaimsPrincipal` creato dal certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="44d4b-129">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="44d4b-129">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="44d4b-130">Nell'esempio precedente viene illustrata la modalità predefinita per aggiungere l'autenticazione del certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="44d4b-131">Il gestore crea un'entità utente usando le proprietà comuni del certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="44d4b-132">Configurare la convalida del certificato</span><span class="sxs-lookup"><span data-stu-id="44d4b-132">Configure certificate validation</span></span>

<span data-ttu-id="44d4b-133">Il `CertificateAuthenticationOptions` gestore dispone di alcune convalide predefinite che rappresentano le convalide minime che è necessario eseguire su un certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="44d4b-134">Ognuna di queste impostazioni è abilitata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="44d4b-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="44d4b-135">AllowedCertificateTypes = concatenato, SelfSigned o tutti (concatenato | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="44d4b-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="44d4b-136">Valore predefinito: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="44d4b-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="44d4b-137">Questo controllo consente di verificare che sia consentito solo il tipo di certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="44d4b-138">Se l'app usa certificati autofirmati, questa opzione deve essere impostata su `CertificateTypes.All` o `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="44d4b-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="44d4b-139">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="44d4b-139">ValidateCertificateUse</span></span>

<span data-ttu-id="44d4b-140">Valore predefinito: `true`</span><span class="sxs-lookup"><span data-stu-id="44d4b-140">Default value: `true`</span></span>

<span data-ttu-id="44d4b-141">Questo controllo verifica che il certificato presentato dal client disponga dell'utilizzo chiavi avanzato (EKU) di autenticazione client o non EKU affatto.</span><span class="sxs-lookup"><span data-stu-id="44d4b-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="44d4b-142">Come le specifiche dicono, se non è specificato alcun EKU, tutti i EKU vengono considerati validi.</span><span class="sxs-lookup"><span data-stu-id="44d4b-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="44d4b-143">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="44d4b-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="44d4b-144">Valore predefinito: `true`</span><span class="sxs-lookup"><span data-stu-id="44d4b-144">Default value: `true`</span></span>

<span data-ttu-id="44d4b-145">Questo controllo verifica che il certificato sia entro il periodo di validità.</span><span class="sxs-lookup"><span data-stu-id="44d4b-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="44d4b-146">Per ogni richiesta, il gestore garantisce che un certificato valido al momento della presentazione non sia scaduto durante la sessione corrente.</span><span class="sxs-lookup"><span data-stu-id="44d4b-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="44d4b-147">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="44d4b-147">RevocationFlag</span></span>

<span data-ttu-id="44d4b-148">Valore predefinito: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="44d4b-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="44d4b-149">Flag che specifica i certificati nella catena controllati per la revoca.</span><span class="sxs-lookup"><span data-stu-id="44d4b-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="44d4b-150">I controlli di revoca vengono eseguiti solo quando il certificato è concatenato a un certificato radice.</span><span class="sxs-lookup"><span data-stu-id="44d4b-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="44d4b-151">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="44d4b-151">RevocationMode</span></span>

<span data-ttu-id="44d4b-152">Valore predefinito: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="44d4b-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="44d4b-153">Flag che specifica la modalità di esecuzione dei controlli di revoca.</span><span class="sxs-lookup"><span data-stu-id="44d4b-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="44d4b-154">La specifica di un controllo online può comportare un ritardo prolungato mentre viene contattata l'autorità di certificazione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="44d4b-155">I controlli di revoca vengono eseguiti solo quando il certificato è concatenato a un certificato radice.</span><span class="sxs-lookup"><span data-stu-id="44d4b-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="44d4b-156">È possibile configurare l'app in modo che richieda un certificato solo su determinati percorsi?</span><span class="sxs-lookup"><span data-stu-id="44d4b-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="44d4b-157">Questa operazione non è possibile.</span><span class="sxs-lookup"><span data-stu-id="44d4b-157">This isn't possible.</span></span> <span data-ttu-id="44d4b-158">Tenere presente che lo scambio di certificati è stato eseguito dall'avvio della conversazione HTTPS, che viene eseguito dal server prima che la prima richiesta venga ricevuta sulla connessione, quindi non è possibile definire l'ambito in base a qualsiasi campo di richiesta.</span><span class="sxs-lookup"><span data-stu-id="44d4b-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="44d4b-159">Eventi gestore</span><span class="sxs-lookup"><span data-stu-id="44d4b-159">Handler events</span></span>

<span data-ttu-id="44d4b-160">Il gestore dispone di due eventi:</span><span class="sxs-lookup"><span data-stu-id="44d4b-160">The handler has two events:</span></span>

* <span data-ttu-id="44d4b-161">`OnAuthenticationFailed`: Viene chiamato se si verifica un'eccezione durante l'autenticazione e consente di rispondere.</span><span class="sxs-lookup"><span data-stu-id="44d4b-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="44d4b-162">`OnCertificateValidated`: Viene chiamato dopo che il certificato è stato convalidato, la convalida è stata superata ed è stata creata un'entità predefinita.</span><span class="sxs-lookup"><span data-stu-id="44d4b-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="44d4b-163">Questo evento consente di eseguire una convalida personalizzata e di aumentare o sostituire l'entità di protezione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="44d4b-164">Gli esempi includono:</span><span class="sxs-lookup"><span data-stu-id="44d4b-164">For examples include:</span></span>
  * <span data-ttu-id="44d4b-165">Determinare se il certificato è noto ai servizi.</span><span class="sxs-lookup"><span data-stu-id="44d4b-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="44d4b-166">Creazione di un'entità personalizzata.</span><span class="sxs-lookup"><span data-stu-id="44d4b-166">Constructing your own principal.</span></span> <span data-ttu-id="44d4b-167">Si consideri l'esempio seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="44d4b-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="44d4b-168">Se il certificato in ingresso non soddisfa la convalida aggiuntiva, chiamare `context.Fail("failure reason")` con un motivo dell'errore.</span><span class="sxs-lookup"><span data-stu-id="44d4b-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="44d4b-169">Per le funzionalità reali, è probabile che si voglia chiamare un servizio registrato nell'inserimento delle dipendenze che si connette a un database o a un altro tipo di archivio utente.</span><span class="sxs-lookup"><span data-stu-id="44d4b-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="44d4b-170">Accedere al servizio usando il contesto passato al delegato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="44d4b-171">Si consideri l'esempio seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="44d4b-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="44d4b-172">Concettualmente, la convalida del certificato è un problema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="44d4b-173">L'aggiunta di un controllo, ad esempio, di un'autorità emittente o di un'identificazione personale in un criterio di autorizzazione, anziché all'interno di `OnCertificateValidated` , è perfettamente accettabile.</span><span class="sxs-lookup"><span data-stu-id="44d4b-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="44d4b-174">Configurare l'host per richiedere i certificati</span><span class="sxs-lookup"><span data-stu-id="44d4b-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="44d4b-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="44d4b-175">Kestrel</span></span>

<span data-ttu-id="44d4b-176">In *Program.cs*configurare gheppio come segue:</span><span class="sxs-lookup"><span data-stu-id="44d4b-176">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="44d4b-177">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="44d4b-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="44d4b-178">IIS</span><span class="sxs-lookup"><span data-stu-id="44d4b-178">IIS</span></span>

<span data-ttu-id="44d4b-179">Completare i passaggi seguenti in Gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="44d4b-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="44d4b-180">Selezionare il sito dalla scheda **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="44d4b-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="44d4b-181">Fare doppio clic sull'opzione **Impostazioni SSL** nella finestra **visualizzazione funzionalità** .</span><span class="sxs-lookup"><span data-stu-id="44d4b-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="44d4b-182">Selezionare la casella di controllo **Richiedi SSL** e selezionare il pulsante di opzione **Richiedi** nella sezione **certificati client** .</span><span class="sxs-lookup"><span data-stu-id="44d4b-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Impostazioni del certificato client in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="44d4b-184">Azure e proxy Web personalizzati</span><span class="sxs-lookup"><span data-stu-id="44d4b-184">Azure and custom web proxies</span></span>

<span data-ttu-id="44d4b-185">Vedere l' [host e distribuire la documentazione](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) per informazioni su come configurare il middleware di invio del certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="44d4b-186">Usare l'autenticazione del certificato in app Web di Azure</span><span class="sxs-lookup"><span data-stu-id="44d4b-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="44d4b-187">Per Azure non è necessaria alcuna configurazione di invio.</span><span class="sxs-lookup"><span data-stu-id="44d4b-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="44d4b-188">Questa configurazione è già presente nel middleware di invio del certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="44d4b-189">A tale scopo, è necessario che CertificateForwardingMiddleware sia presente.</span><span class="sxs-lookup"><span data-stu-id="44d4b-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="44d4b-190">Usare l'autenticazione del certificato in proxy Web personalizzati</span><span class="sxs-lookup"><span data-stu-id="44d4b-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="44d4b-191">Il `AddCertificateForwarding` metodo viene usato per specificare:</span><span class="sxs-lookup"><span data-stu-id="44d4b-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="44d4b-192">Nome dell'intestazione del client.</span><span class="sxs-lookup"><span data-stu-id="44d4b-192">The client header name.</span></span>
* <span data-ttu-id="44d4b-193">Modalità di caricamento del certificato (utilizzando la `HeaderConverter` proprietà).</span><span class="sxs-lookup"><span data-stu-id="44d4b-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="44d4b-194">Nei proxy Web personalizzati il certificato viene passato come intestazione di richiesta personalizzata, ad esempio `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="44d4b-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="44d4b-195">Per usarlo, configurare l'invio di certificati in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44d4b-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="44d4b-196">Il `Startup.Configure` metodo aggiunge quindi il middleware.</span><span class="sxs-lookup"><span data-stu-id="44d4b-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="44d4b-197">`UseCertificateForwarding`viene chiamato prima delle chiamate a `UseAuthentication` e `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="44d4b-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="44d4b-198">Una classe separata può essere usata per implementare la logica di convalida.</span><span class="sxs-lookup"><span data-stu-id="44d4b-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="44d4b-199">Poiché in questo esempio viene usato lo stesso certificato autofirmato, assicurarsi che sia possibile usare solo il certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="44d4b-200">Verificare che le identificazioni personali del certificato client e del certificato del server corrispondano; in caso contrario, è possibile usare qualsiasi certificato e sarà sufficiente per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="44d4b-201">Viene usato all'interno del `AddCertificate` metodo.</span><span class="sxs-lookup"><span data-stu-id="44d4b-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="44d4b-202">È anche possibile convalidare l'oggetto o l'autorità emittente qui se si usano certificati intermedi o figlio.</span><span class="sxs-lookup"><span data-stu-id="44d4b-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="44d4b-203">Implementare un HttpClient usando un certificato e HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="44d4b-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="44d4b-204">È possibile aggiungere HttpClientHandler direttamente nel costruttore della classe HttpClient.</span><span class="sxs-lookup"><span data-stu-id="44d4b-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="44d4b-205">Quando si creano istanze di HttpClient è necessario prestare attenzione.</span><span class="sxs-lookup"><span data-stu-id="44d4b-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="44d4b-206">Il HttpClient invierà quindi il certificato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="44d4b-206">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="44d4b-207">Implementare un HttpClient usando un certificato e un HttpClient denominato da IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="44d4b-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="44d4b-208">Nell'esempio seguente viene aggiunto un certificato client a un HttpClientHandler usando la proprietà ClientCertificates dal gestore.</span><span class="sxs-lookup"><span data-stu-id="44d4b-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="44d4b-209">Questo gestore può quindi essere utilizzato in un'istanza denominata di un HttpClient utilizzando il metodo ConfigurePrimaryHttpMessageHandler.</span><span class="sxs-lookup"><span data-stu-id="44d4b-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="44d4b-210">Questa configurazione è configurata nella classe Startup nel metodo ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="44d4b-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="44d4b-211">IHttpClientFactory può quindi essere usato per ottenere l'istanza denominata con il gestore e il certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="44d4b-212">Il Metodo CreateClient con il nome del client definito nella classe Startup viene usato per ottenere l'istanza.</span><span class="sxs-lookup"><span data-stu-id="44d4b-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="44d4b-213">La richiesta HTTP può essere inviata utilizzando il client come richiesto.</span><span class="sxs-lookup"><span data-stu-id="44d4b-213">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="44d4b-214">Se il certificato corretto viene inviato al server, vengono restituiti i dati.</span><span class="sxs-lookup"><span data-stu-id="44d4b-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="44d4b-215">Se non viene inviato alcun certificato o il certificato errato, viene restituito un codice di stato HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="44d4b-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="44d4b-216">Creare certificati in PowerShell</span><span class="sxs-lookup"><span data-stu-id="44d4b-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="44d4b-217">La creazione dei certificati è la parte più difficile per la configurazione di questo flusso.</span><span class="sxs-lookup"><span data-stu-id="44d4b-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="44d4b-218">È possibile creare un certificato radice usando il `New-SelfSignedCertificate` cmdlet di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="44d4b-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="44d4b-219">Quando si crea il certificato, usare una password complessa.</span><span class="sxs-lookup"><span data-stu-id="44d4b-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="44d4b-220">È importante aggiungere il `KeyUsageProperty` parametro e il `KeyUsage` parametro come illustrato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="44d4b-221">Crea CA radice</span><span class="sxs-lookup"><span data-stu-id="44d4b-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="44d4b-222">Il `-DnsName` valore del parametro deve corrispondere alla destinazione di distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="44d4b-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="44d4b-223">Ad esempio, "localhost" per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="44d4b-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="44d4b-224">Installare nella radice attendibile</span><span class="sxs-lookup"><span data-stu-id="44d4b-224">Install in the trusted root</span></span>

<span data-ttu-id="44d4b-225">Il certificato radice deve essere considerato attendibile nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="44d4b-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="44d4b-226">Un certificato radice che non è stato creato da un'autorità di certificazione non sarà considerato attendibile per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="44d4b-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="44d4b-227">Il collegamento seguente illustra come eseguire questa operazione in Windows:</span><span class="sxs-lookup"><span data-stu-id="44d4b-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="44d4b-228">Certificato intermedio</span><span class="sxs-lookup"><span data-stu-id="44d4b-228">Intermediate certificate</span></span>

<span data-ttu-id="44d4b-229">È ora possibile creare un certificato intermedio dal certificato radice.</span><span class="sxs-lookup"><span data-stu-id="44d4b-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="44d4b-230">Questa operazione non è necessaria per tutti i casi d'uso, ma potrebbe essere necessario creare molti certificati o attivare o disabilitare gruppi di certificati.</span><span class="sxs-lookup"><span data-stu-id="44d4b-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="44d4b-231">Il `TextExtension` parametro è necessario per impostare la lunghezza del percorso nei vincoli di base del certificato.</span><span class="sxs-lookup"><span data-stu-id="44d4b-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="44d4b-232">Il certificato intermedio può quindi essere aggiunto al certificato intermedio attendibile nel sistema host Windows.</span><span class="sxs-lookup"><span data-stu-id="44d4b-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="44d4b-233">Crea certificato figlio da certificato intermedio</span><span class="sxs-lookup"><span data-stu-id="44d4b-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="44d4b-234">Un certificato figlio può essere creato dal certificato intermedio.</span><span class="sxs-lookup"><span data-stu-id="44d4b-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="44d4b-235">Si tratta dell'entità finale e non è necessario creare più certificati figlio.</span><span class="sxs-lookup"><span data-stu-id="44d4b-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="44d4b-236">Crea certificato figlio dal certificato radice</span><span class="sxs-lookup"><span data-stu-id="44d4b-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="44d4b-237">È anche possibile creare un certificato figlio direttamente dal certificato radice.</span><span class="sxs-lookup"><span data-stu-id="44d4b-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="44d4b-238">Esempio di certificato radice-intermedio-certificato</span><span class="sxs-lookup"><span data-stu-id="44d4b-238">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="44d4b-239">Quando si utilizzano i certificati radice, intermedi o figlio, è possibile convalidare i certificati utilizzando l'identificazione personale o PublicKey come richiesto.</span><span class="sxs-lookup"><span data-stu-id="44d4b-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
