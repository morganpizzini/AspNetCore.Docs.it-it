* <span data-ttu-id="f6c1d-101">Considerare attendibile il certificato di sviluppo HTTPS eseguendo il comando riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="f6c1d-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="f6c1d-102">Il comando precedente non funziona in Linux.</span><span class="sxs-lookup"><span data-stu-id="f6c1d-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="f6c1d-103">Vedere la documentazione della distribuzione di Linux per l'attendibilità di un certificato.</span><span class="sxs-lookup"><span data-stu-id="f6c1d-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="f6c1d-104">Il comando precedente consente di visualizzare la finestra di dialogo seguente:</span><span class="sxs-lookup"><span data-stu-id="f6c1d-104">The preceding command displays the following dialog:</span></span>

  ![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

* <span data-ttu-id="f6c1d-106">Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="f6c1d-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="f6c1d-107">Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="f6c1d-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
