<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Disabilitare la verifica dell'account predefinita

Con i modelli predefiniti, l'utente viene reindirizzato al `Account.RegisterConfirmation` dove è possibile selezionare un collegamento per confermare l'account. Il valore predefinito `Account.RegisterConfirmation` viene usato ***solo*** per i test. la verifica automatica dell'account deve essere disabilitata in un'app di produzione.

Per richiedere un account confermato e impedire l'accesso immediato alla registrazione, impostare `DisplayConfirmAccountLink = false` in */areas/Identity/Pages/account/RegisterConfirmation.cshtml.cs*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]