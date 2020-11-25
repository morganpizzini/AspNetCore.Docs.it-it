* Considerare attendibile il certificato di sviluppo HTTPS eseguendo il comando riportato di seguito:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Il comando precedente non funziona in Linux. Vedere la documentazione della distribuzione di Linux per l'attendibilità di un certificato.

  Il comando precedente consente di visualizzare la finestra di dialogo seguente:

  ![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

* Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.

  Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]