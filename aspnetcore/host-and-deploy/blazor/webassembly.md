---
title: "host and deploy ASP.NET Core Blazor webassembly" Author: guardrex Description: "informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine di GitHub".
monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 05/28/2020 no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: host-and-deploy/Blazer/webassembly

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Ospitare e distribuire ASP.NET Core Blazor Webassembly

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)

Con il [ Blazor modello di hosting webassembly](xref:blazor/hosting-models#blazor-webassembly):

* L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.
* L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.

Sono supportate le strategie di distribuzione seguenti:

* L' Blazor app viene gestita da un'app ASP.NET Core. Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).
* L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app. Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor app webassembly come una sub-app IIS.

## <a name="precompression"></a>Precompressione

Quando Blazor viene pubblicata un'app webassembly, l'output viene precompresso per ridurre le dimensioni dell'app e rimuovere la necessità di compressione del runtime. Vengono utilizzati gli algoritmi di compressione seguenti:

* [Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)
* [Gzip](https://tools.ietf.org/html/rfc1952))

Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

Per la configurazione della compressione IIS *Web. config* , vedere la sezione relativa alla [compressione IIS: Brotli e gzip](#brotli-and-gzip-compression) .

## <a name="rewrite-urls-for-correct-routing"></a>Riscrivere gli URL per il routing corretto

Il routing delle richieste per i componenti della pagina in un' Blazor app webassembly non è semplice come il routing delle richieste in un Blazor Server, un'app ospitata. Si consideri un' Blazor app webassembly con due componenti:

* *Main. Razor*: carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).
* *About. Razor*: `About` Component.

Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):

1. Il browser invia una richiesta.
1. Viene restituita la pagina predefinita, di solito *index.html*.
1. *index.html* esegue il bootstrap dell'app.
1. Blazoril router viene caricato e il componente viene sottoposto a Razor `Main` rendering.

Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` . Tutte le richieste per gli endpoint interni *nell' Blazor app webassembly* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet. Le richieste vengono gestite internamente dal router.

Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo. La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.

Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*. Quando viene restituito *index. html* , il router dell'app Blazor accetta e risponde con la risorsa corretta.

Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app. Per ulteriori informazioni, vedere la sezione [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Distribuzione ospitata con ASP.NET Core

Una *distribuzione ospitata* serve l' Blazor app webassembly nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.

L' Blazor app webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server. Le due app vengono distribuite insieme. È necessario un server Web in grado di ospitare un'app ASP.NET Core. Per una distribuzione ospitata, Visual Studio include il modello di progetto di ** Blazor app webassembly** ( `blazorwasm` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ) con l'opzione **Hosted** selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).

Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.

Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Distribuzione autonoma

Una *distribuzione autonoma* serve l' Blazor app webassembly come un set di file statici richiesti direttamente dai client. Qualsiasi file server statico è in grado di gestire l' Blazor app.

Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*

### <a name="azure-app-service"></a>Servizio app di Azure

BlazorLe app webassembly possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).

La distribuzione Blazor di un'app webassembly autonoma nel servizio app Azure per Linux non è attualmente supportata. Un'immagine server Linux per ospitare l'app non è disponibile in questo momento. Il lavoro è in corso per abilitare questo scenario.

### <a name="iis"></a>IIS

IIS è un file server statico idoneo per le Blazor app. Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*. Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.

#### <a name="webconfig"></a>web.config

Quando Blazor viene pubblicato un progetto, viene creato un file *Web. config* con la seguente configurazione di IIS:

* Vengono impostati tipi MIME per le estensioni di file seguenti:
  * *. dll*:`application/octet-stream`
  * *. JSON*:`application/json`
  * *. WASM*:`application/wasm`
  * *. WOFF*:`application/font-woff`
  * *. woff2*:`application/font-woff`
* Viene abilitata la compressione HTTP per i tipi MIME seguenti:
  * `application/octet-stream`
  * `application/wasm`
* Vengono stabilite le regole URL Rewrite Module:
  * Serve la sottodirectory in cui risiedono gli asset statici dell'app (*wwwroot/{Path richiesto}*).
  * Creare il routing di fallback SPA in modo che le richieste di risorse non di file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Usare un file Web. config personalizzato

Per usare un file *Web. config* personalizzato, inserire il file *Web. config* personalizzato alla radice della cartella del progetto e pubblicare il progetto.

#### <a name="install-the-url-rewrite-module"></a>Installare URL Rewrite Module

[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL. Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS). Il modulo deve essere scaricato dal sito Web IIS. Usare Installazione guidata piattaforma Web per installare il modulo:

1. In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web. Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.
1. Copiare il programma di installazione nel server. Eseguire il programma di installazione. Selezionare il pulsante **Installa** e accettare le condizioni di licenza. Al termine dell'installazione non è necessario un riavvio del server.

#### <a name="configure-the-website"></a>Configurare il sito Web

Impostare il **percorso fisico** del sito Web sulla cartella dell'app. La cartella contiene:

* Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.
* Cartella degli asset statici dell'app.

#### <a name="host-as-an-iis-sub-app"></a>Ospitare come applicazione secondaria IIS

Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:

* Disabilitare il gestore del modulo ASP.NET Core ereditato.

  Rimuovere il gestore nel Blazor file *Web. config* pubblicato dall'app aggiungendo una `<handlers>` sezione al file:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path). Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.

#### <a name="brotli-and-gzip-compression"></a>Compressione Brotli e gzip

IIS può essere configurato tramite *Web. config* per gestire gli asset compressi Brotli o gzip Blazor . Per una configurazione di esempio, vedere [Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Risoluzione dei problemi

Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module. Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS. In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.

Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Archiviazione di Azure

L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor . Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.

Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:

* Impostare **Nome del documento di indice** su `index.html`.
* Impostare **Percorso del documento di errore** su `index.html`. Razori componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB. Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**. Viene restituito il BLOB *index. html* e il Blazor router carica ed elabora il percorso.

Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:

* Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.
* Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.

  In Storage Explorer (portale di Azure) per ogni file:
  
  1. Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.
  1. Impostare **ContentType** e selezionare il pulsante **Salva** .

Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Il file *nginx. conf* seguente è stato semplificato per illustrare come configurare Nginx per inviare il file *index. html* ogni volta che non riesce a trovare un file corrispondente sul disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).

### <a name="nginx-in-docker"></a>Nginx in Docker

Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine. Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.

Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Per distribuire un' Blazor app webassembly in CentOS 7 o versione successiva:

1. Creare il file di configurazione Apache. L'esempio seguente è un file di configurazione semplificato (*blazorapp. config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.

1. Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).

1. Riavviare il servizio Apache.

Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Pagine di GitHub

Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*. Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)). È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).

Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*. Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.

## <a name="host-configuration-values"></a>Valori di configurazione dell'host

Le [ Blazor app webassembly](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.

### <a name="content-root"></a>Radice del contenuto

L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)). Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Base del percorso

L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione). Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app. Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`. Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.

* Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi. Dalla directory dell'app, eseguire:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**. Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**. Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Configurare il linker

Blazoresegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output. Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Caricamento di risorse di avvio personalizzate

Un' Blazor app webassembly può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito. Utilizzare `loadBootResource` per gli scenari seguenti:

* Consentire agli utenti di caricare risorse statiche, ad esempio i dati del fuso orario o *dotnet. WASM* da una rete CDN.
* Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.
* Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.

`loadBootResource`i parametri vengono visualizzati nella tabella seguente.

| Parametro    | Description |
| ------------ | ----------- |
| `type`       | Tipo di risorsa. Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata` |
| `name`       | Nome della risorsa. |
| `defaultUri` | URI relativo o assoluto della risorsa. |
| `integrity`  | Stringa di integrità che rappresenta il contenuto previsto nella risposta. |

`loadBootResource`restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:

* Stringa URI. Nell'esempio seguente (*wwwroot/index.html*), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :

  * *dotnet. \* .. JS*
  * *dotnet. WASM*
  * Dati TimeZone

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.

  L'esempio seguente (*wwwroot/index.html*) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, che comporta il comportamento di caricamento predefinito.

Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser. Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.

È sufficiente specificare i tipi per i comportamenti personalizzati. I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.

## <a name="change-the-filename-extension-of-dll-files"></a>Modificare l'estensione di file DLL

Se è necessario modificare le estensioni del nome file dei file con *estensione dll* pubblicati dell'app, seguire le istruzioni riportate in questa sezione.

Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare i file con estensione *dll* per usare un'estensione di file diversa. Specificare come destinazione i file con *estensione dll* nella directory *wwwroot* dell'output pubblicato dell'app (ad esempio, *{Content root}/bin/Release/netstandard2.1/Publish/wwwroot*).

Negli esempi seguenti, i file con estensione *dll* vengono rinominati per l'utilizzo dell'estensione di file *bin* .

In Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

In Linux o macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Per usare un'estensione di file diversa da *. bin*, sostituire *. bin* nei comandi precedenti.

Per risolvere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* , adottare uno degli approcci seguenti:

* Rimuovere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* . La compressione è disabilitata con questo approccio.
* Ricomprimere il file *blazer. boot. JSON* aggiornato.

Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso. Rimuovere o ricomprimere *wwwroot/service-worker-assets. js. br* e *wwwroot/service-worker-assets. js. gz*. In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.

Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.

*ChangeDLLExtensions. ps1:*:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Per fornire commenti e suggerimenti, vedere [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).
 
