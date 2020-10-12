<span data-ttu-id="b10d0-101">Quando si usa un'API Server registrata con AAD e la registrazione AAD dell'app si trova in un tenant che si basa su un dominio del server di [pubblicazione non verificato](/azure/active-directory/develop/howto-configure-publisher-domain), l'URI ID app dell'app per le API del server non è, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ma è nel formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="b10d0-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="b10d0-102">In tal caso, l'ambito del token di accesso predefinito in `Program.Main` ( `Program.cs` ) dell' *`Client`* applicazione sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b10d0-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="b10d0-103">Per configurare l'app per le API server per un gruppo di destinatari corrispondente, impostare il `Audience` nel *`Server`* file di impostazioni dell'app `appsettings.json` per le API () in modo che corrisponda ai destinatari dell'app forniti dal portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="b10d0-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="b10d0-104">Nella configurazione precedente, la fine del valore non `Audience` include l' **not** ambito predefinito `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="b10d0-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="b10d0-105">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b10d0-105">Example:</span></span>

<span data-ttu-id="b10d0-106">`Program.Main` ( `Program.cs` ) dell' *`Client`* app:</span><span class="sxs-lookup"><span data-stu-id="b10d0-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="b10d0-107">Configurare il *`Server`* file di impostazioni dell'app per le API ( `appsettings.json` ) con un gruppo di destinatari corrispondente ( `Audience` ):</span><span class="sxs-lookup"><span data-stu-id="b10d0-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="b10d0-108">Nell'esempio precedente, la fine del valore non `Audience` include l' **not** ambito predefinito `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="b10d0-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
