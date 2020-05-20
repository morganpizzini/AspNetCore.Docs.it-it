---
title:' Manage Users and groups in SignalR ' Author: Description:' Overview of ASP.NET Core SignalR User and Group Management .'
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---

# <a name="manage-users-and-groups-in-signalr"></a>Gestire utenti e gruppi inSignalR

Di [Brennan Conroy](https://github.com/BrennanConroy)

SignalRconsente l'invio di messaggi a tutte le connessioni associate a un utente specifico, oltre che ai gruppi denominati di connessioni.

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Utenti inSignalR

Un singolo utente in SignalR può avere più connessioni a un'app. Ad esempio, un utente potrebbe essere connesso sul desktop e sul telefono. Ogni dispositivo ha una SignalR connessione separata, ma tutti sono associati allo stesso utente. Se un messaggio viene inviato all'utente, tutte le connessioni associate a tale utente riceveranno il messaggio. È possibile accedere all'identificatore utente per una connessione tramite la `Context.UserIdentifier` proprietà nell'hub.

Per impostazione predefinita, SignalR utilizza l'oggetto `ClaimTypes.NameIdentifier` dall'oggetto associato alla `ClaimsPrincipal` connessione come identificatore utente. Per personalizzare questo comportamento, vedere [usare le attestazioni per personalizzare la gestione delle identità](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Inviare un messaggio a un utente specifico passando l'identificatore utente alla `User` funzione in un metodo Hub, come illustrato nell'esempio seguente:

> [!NOTE]
> L'identificatore utente distingue tra maiuscole e minuscole.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Gruppi inSignalR

Un gruppo è una raccolta di connessioni associate a un nome. I messaggi possono essere inviati a tutte le connessioni in un gruppo. I gruppi sono il metodo consigliato per l'invio a una connessione o a più connessioni perché i gruppi sono gestiti dall'applicazione. Una connessione può essere un membro di più gruppi. I gruppi sono ideali per qualcosa di simile a un'applicazione di chat, in cui ogni stanza può essere rappresentata come un gruppo. Le connessioni vengono aggiunte o rimosse dai gruppi tramite `AddToGroupAsync` i `RemoveFromGroupAsync` metodi e.

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

L'appartenenza al gruppo non viene mantenuta quando si riconnette una connessione. La connessione deve essere nuovamente aggiunta al gruppo quando viene ristabilita. Non è possibile contare i membri di un gruppo, poiché queste informazioni non sono disponibili se l'applicazione viene ridimensionata in più server.

Per proteggere l'accesso alle risorse durante l'uso di gruppi, usare la funzionalità di [autenticazione e autorizzazione](xref:signalr/authn-and-authz) in ASP.NET Core. Se un utente viene aggiunto a un gruppo solo quando le credenziali sono valide per il gruppo, i messaggi inviati a tale gruppo passeranno solo agli utenti autorizzati. Tuttavia, i gruppi non sono una funzionalità di sicurezza. Le attestazioni di autenticazione hanno funzionalità che non vengono eseguite dai gruppi, ad esempio la scadenza e la revoca. Se l'autorizzazione dell'utente per accedere al gruppo viene revocata, l'app deve rimuovere l'utente dal gruppo in modo esplicito.

> [!NOTE]
> I nomi dei gruppi fanno distinzione tra maiuscole e minuscole

## <a name="related-resources"></a>Risorse correlate

* [Attività iniziali](xref:tutorials/signalr)
* [Hub](xref:signalr/hubs)
* [Pubblicazione in Azure](xref:signalr/publish-to-azure-web-app)
