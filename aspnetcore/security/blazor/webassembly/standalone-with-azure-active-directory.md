---
title: "proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory" autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>Proteggere un' Blazor app ASP.NET Core webassembly autonoma con Azure Active Directory

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Per creare un' Blazor app webassembly autonoma che usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione:

[Creare un'applicazione Web e un tenant di AAD](/azure/active-directory/develop/v2-overview):

Registrare un'app AAD nell'area **Azure Active Directory**  >  **registrazioni app** del portale di Azure:

1. Specificare un **nome** per l'app, ad esempio ** Blazor AAD autonomo**.
1. Scegliere i **tipi di conto supportati**. È possibile selezionare gli **account in questa directory organizzativa solo** per questa esperienza.
1. Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare l'URI di reindirizzamento seguente: `https://localhost:{PORT}/authentication/login-callback` . La porta predefinita per un'app in esecuzione su gheppio è 5001. Per IIS Express, la porta generata in modo casuale si trova nelle proprietà dell'app nel pannello **debug** .
1. Disabilitare la **Permissions**  >  casella di controllo autorizzazioni**concessi da amministratore a OpenID e autorizzazioni offline_access** .
1. Selezionare **Registra**.

Registrare le seguenti informazioni:

* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111` )
* ID directory (ID tenant) (ad esempio, `22222222-2222-2222-2222-222222222222` )

In **Authentication**  >  **configurazioni piattaforma**di autenticazione  >  **Web**:

1. Verificare che l' **URI di reindirizzamento** di `https://localhost:{PORT}/authentication/login-callback` sia presente.
1. Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.
1. Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.
1. Fare clic sul pulsante **Salva**.

Creare l'app. Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ). Il nome della cartella diventa anche parte del nome del progetto.

Dopo aver creato l'app, dovrebbe essere possibile:

* Accedere all'app usando un account utente di AAD.
* Richiedere i token di accesso per le API Microsoft. Per altre informazioni, vedere:
  * [Ambiti del token di accesso](#access-token-scopes)
  * [Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione ( `SingleOrg` ), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ). Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

Il `Microsoft.Authentication.WebAssembly.Msal` pacchetto aggiunge il `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pacchetto all'app in modo transitivo.

## <a name="authentication-service-support"></a>Supporto del servizio di autenticazione

Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il `AddMsalAuthentication` metodo di estensione fornito dal `Microsoft.Authentication.WebAssembly.Msal` pacchetto. Questo metodo configura i servizi necessari per l'interazione dell'app con il Identity provider (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

Il `AddMsalAuthentication` metodo accetta un callback per configurare i parametri necessari per autenticare un'app. Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD.

La configurazione viene fornita dal file *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Esempio:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>Ambiti del token di accesso

Il Blazor modello webassembly non configura automaticamente l'app per richiedere un token di accesso per un'API protetta. Per eseguire il provisioning di un token di accesso come parte del flusso di accesso, aggiungere l'ambito agli ambiti dei token di accesso predefiniti di `MsalProviderOptions` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Per ulteriori informazioni, vedere le sezioni seguenti dell'articolo *scenari aggiuntivi* :

* [Richiedere token di accesso aggiuntivi](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Connetti token alle richieste in uscita](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/blazor/webassembly/additional-scenarios>
* [Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentazione di Microsoft Identity Platform](/azure/active-directory/develop/)
