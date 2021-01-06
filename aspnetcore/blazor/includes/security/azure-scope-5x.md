Quando si usa un'API Server registrata con AAD e la registrazione AAD dell'app si trova in un tenant che si basa su un dominio del server di [pubblicazione non verificato](/azure/active-directory/develop/howto-configure-publisher-domain), l'URI ID app dell'app per le API del server non è, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ma è nel formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . In tal caso, l'ambito del token di accesso predefinito in `Program.Main` ( `Program.cs` ) dell' *`Client`* applicazione sarà simile al seguente:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Per configurare l'app per le API server per un gruppo di destinatari corrispondente, impostare il `Audience` nel *`Server`* file di impostazioni dell'app `appsettings.json` per le API () in modo che corrisponda ai destinatari dell'app forniti dal portale di Azure:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

Nella configurazione precedente, la fine del valore non `Audience` include l'  ambito predefinito `/{DEFAULT SCOPE}` .

Esempio:

`Program.Main` ( `Program.cs` ) dell' *`Client`* app:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Configurare il *`Server`* file di impostazioni dell'app per le API ( `appsettings.json` ) con un gruppo di destinatari corrispondente ( `Audience` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

Nell'esempio precedente, la fine del valore non `Audience` include l'  ambito predefinito `/API.Access` .
