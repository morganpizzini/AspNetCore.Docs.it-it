# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Premere CTRL+F5 per l'esecuzione senza il debugger.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Premere **CTRL+F5** per l'esecuzione senza il debugger.

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`. La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Da Visual Studio, premere **Opt-Cmd-Return** per eseguire senza il debugger. In alternativa, passare alla barra dei menu e passare a **Esegui>di avvio senza eseguire debug**.

  Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.

<!-- End of VS tabs -->

---