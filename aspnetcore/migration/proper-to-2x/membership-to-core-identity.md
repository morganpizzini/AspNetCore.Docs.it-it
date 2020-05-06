---
title: Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0Identity
author: isaac2004
description: Informazioni su come eseguire la migrazione di app ASP.NET esistenti usando l'autenticazione Identitydi appartenenza a ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774509"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="671a5-103">Eseguire la migrazione da ASP.NET Membership Authentication a ASP.NET Core 2,0 Identity</span><span class="sxs-lookup"><span data-stu-id="671a5-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="671a5-104">Di [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="671a5-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="671a5-105">Questo articolo illustra la migrazione dello schema del database per le app ASP.NET usando l'autenticazione di appartenenza a ASP.NET Core 2,0 identità.</span><span class="sxs-lookup"><span data-stu-id="671a5-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="671a5-106">Questo documento illustra i passaggi necessari per eseguire la migrazione dello schema del database per le app basate sull'appartenenza a ASP.NET allo schema del database usato per ASP.NET Core identità.</span><span class="sxs-lookup"><span data-stu-id="671a5-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="671a5-107">Per altre informazioni sulla migrazione dall'autenticazione basata sull'appartenenza a ASP.NET a ASP.NET Identity, vedere [eseguire la migrazione di un'app esistente dall'appartenenza a SQL a ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="671a5-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="671a5-108">Per ulteriori informazioni sull'identità ASP.NET Core, vedere [Introduzione all'identità in ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="671a5-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="671a5-109">Revisione dello schema di appartenenza</span><span class="sxs-lookup"><span data-stu-id="671a5-109">Review of Membership schema</span></span>

<span data-ttu-id="671a5-110">Prima di ASP.NET 2,0, gli sviluppatori avevano il compito di creare l'intero processo di autenticazione e autorizzazione per le proprie app.</span><span class="sxs-lookup"><span data-stu-id="671a5-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="671a5-111">Con ASP.NET 2,0 è stata introdotta l'appartenenza, che fornisce una soluzione standard per la gestione della sicurezza nelle app ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="671a5-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="671a5-112">Gli sviluppatori sono ora in grado di eseguire il bootstrap di uno schema in un database di SQL Server con il comando [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="671a5-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="671a5-113">Dopo aver eseguito questo comando, nel database sono state create le tabelle seguenti.</span><span class="sxs-lookup"><span data-stu-id="671a5-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabelle di appartenenza](identity/_static/membership-tables.png)

<span data-ttu-id="671a5-115">Per eseguire la migrazione di app esistenti a ASP.NET Core 2,0 identità, è necessario eseguire la migrazione dei dati in queste tabelle alle tabelle utilizzate dal nuovo schema di identità.</span><span class="sxs-lookup"><span data-stu-id="671a5-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="671a5-116">Schema Identity 2,0 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="671a5-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="671a5-117">ASP.NET Core 2,0 segue il principio di [identità](/aspnet/identity/index) introdotto in ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="671a5-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="671a5-118">Sebbene il principio sia condiviso, l'implementazione tra i Framework è diversa, anche tra le versioni di ASP.NET Core (vedere [eseguire la migrazione dell'autenticazione e dell'identità al ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="671a5-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="671a5-119">Il modo più rapido per visualizzare lo schema per ASP.NET Core identità di 2,0 consiste nel creare una nuova app ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="671a5-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="671a5-120">Seguire questa procedura in Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="671a5-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="671a5-121">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="671a5-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="671a5-122">Creare un nuovo progetto di **applicazione Web di ASP.NET Core** denominato *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="671a5-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="671a5-123">Selezionare **ASP.NET Core 2,0** nell'elenco a discesa, quindi selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="671a5-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="671a5-124">Questo modello produce un'app [Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="671a5-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="671a5-125">Prima di fare clic su **OK**, fare clic su **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="671a5-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="671a5-126">Scegliere gli **account utente singoli** per i modelli di identità.</span><span class="sxs-lookup"><span data-stu-id="671a5-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="671a5-127">Infine, fare clic su **OK**, quindi su **OK**.</span><span class="sxs-lookup"><span data-stu-id="671a5-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="671a5-128">Visual Studio crea un progetto usando il modello di identità ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="671a5-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="671a5-129">Selezionare **strumenti** > gestione pacchetti**NuGet** > **console di gestione** pacchetti per aprire la finestra **console di gestione pacchetti** (PMC).</span><span class="sxs-lookup"><span data-stu-id="671a5-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="671a5-130">Passare alla radice del progetto in PMC ed eseguire il comando [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="671a5-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="671a5-131">ASP.NET Core 2,0 identità utilizza EF Core per interagire con il database che archivia i dati di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="671a5-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="671a5-132">Per consentire il funzionamento dell'app appena creata, è necessario che sia presente un database per archiviare questi dati.</span><span class="sxs-lookup"><span data-stu-id="671a5-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="671a5-133">Dopo la creazione di una nuova app, il modo più rapido per esaminare lo schema in un ambiente di database consiste nel creare il database usando [migrazioni EF Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="671a5-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="671a5-134">Questo processo crea un database, localmente o altrove, che simula lo schema.</span><span class="sxs-lookup"><span data-stu-id="671a5-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="671a5-135">Per ulteriori informazioni, consultare la documentazione precedente.</span><span class="sxs-lookup"><span data-stu-id="671a5-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="671a5-136">EF Core comandi usano la stringa di connessione per il database specificato in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="671a5-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="671a5-137">La stringa di connessione seguente è destinata a un database in *localhost* denominato *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="671a5-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="671a5-138">In questa impostazione EF Core è configurato per l'utilizzo della `DefaultConnection` stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="671a5-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="671a5-139">Selezionare **Visualizza** > **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="671a5-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="671a5-140">Espandere il nodo corrispondente al nome del database specificato nella `ConnectionStrings:DefaultConnection` proprietà di *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="671a5-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="671a5-141">Il `Update-Database` comando ha creato il database specificato con lo schema e tutti i dati necessari per l'inizializzazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="671a5-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="671a5-142">Nell'immagine seguente viene illustrata la struttura della tabella creata con i passaggi precedenti.</span><span class="sxs-lookup"><span data-stu-id="671a5-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Tabelle di identità](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="671a5-144">Migrazione dello schema</span><span class="sxs-lookup"><span data-stu-id="671a5-144">Migrate the schema</span></span>

<span data-ttu-id="671a5-145">Le strutture e i campi delle tabelle e dei campi per l'appartenenza e l'identità del ASP.NET Core presentano differenze minime.</span><span class="sxs-lookup"><span data-stu-id="671a5-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="671a5-146">Il modello è stato modificato in modo sostanziale per l'autenticazione/autorizzazione con ASP.NET e app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="671a5-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="671a5-147">Gli oggetti chiave ancora usati con Identity sono *utenti* e *ruoli*.</span><span class="sxs-lookup"><span data-stu-id="671a5-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="671a5-148">Di seguito sono riportate le tabelle di mapping per *utenti*, *ruoli*e *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="671a5-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="671a5-149">Utenti</span><span class="sxs-lookup"><span data-stu-id="671a5-149">Users</span></span>

|<span data-ttu-id="671a5-150">*Identity<br>(dbo. AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="671a5-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="671a5-151">*Appartenenza<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="671a5-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="671a5-152">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-152">**Field Name**</span></span>                 |<span data-ttu-id="671a5-153">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-153">**Type**</span></span>|<span data-ttu-id="671a5-154">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-154">**Field Name**</span></span>                                    |<span data-ttu-id="671a5-155">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="671a5-156">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="671a5-157">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="671a5-158">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="671a5-159">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="671a5-160">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="671a5-161">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="671a5-162">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="671a5-163">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="671a5-164">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="671a5-165">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="671a5-166">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="671a5-167">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="671a5-168">bit</span><span class="sxs-lookup"><span data-stu-id="671a5-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="671a5-169">bit</span><span class="sxs-lookup"><span data-stu-id="671a5-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="671a5-170">Non tutti i mapping dei campi assomigliano a relazioni uno-a-uno dall'appartenenza a ASP.NET Core identità.</span><span class="sxs-lookup"><span data-stu-id="671a5-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="671a5-171">La tabella precedente accetta lo schema utente di appartenenza predefinito e ne esegue il mapping allo schema di identità del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="671a5-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="671a5-172">È necessario eseguire il mapping manuale di tutti gli altri campi personalizzati usati per l'appartenenza.</span><span class="sxs-lookup"><span data-stu-id="671a5-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="671a5-173">In questo mapping non è disponibile alcuna mappa per le password, perché i criteri password e i Salt delle password non vengono migrati tra i due.</span><span class="sxs-lookup"><span data-stu-id="671a5-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="671a5-174">**È consigliabile lasciare la password null e richiedere agli utenti di reimpostare le password.**</span><span class="sxs-lookup"><span data-stu-id="671a5-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="671a5-175">In ASP.NET Core identità, `LockoutEnd` deve essere impostato su una data futura se l'utente è bloccato. Questa operazione viene mostrata nello script di migrazione.</span><span class="sxs-lookup"><span data-stu-id="671a5-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="671a5-176">Ruoli</span><span class="sxs-lookup"><span data-stu-id="671a5-176">Roles</span></span>

|<span data-ttu-id="671a5-177">*Identity<br>(dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="671a5-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="671a5-178">*Appartenenza<br>(dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="671a5-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="671a5-179">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-179">**Field Name**</span></span>                 |<span data-ttu-id="671a5-180">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-180">**Type**</span></span>|<span data-ttu-id="671a5-181">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-181">**Field Name**</span></span>   |<span data-ttu-id="671a5-182">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="671a5-183">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-183">string</span></span>  |`RoleId`         | <span data-ttu-id="671a5-184">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="671a5-185">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-185">string</span></span>  |`RoleName`       | <span data-ttu-id="671a5-186">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="671a5-187">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="671a5-188">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="671a5-189">Ruoli utente</span><span class="sxs-lookup"><span data-stu-id="671a5-189">User Roles</span></span>

|<span data-ttu-id="671a5-190">*Identity<br>(dbo. AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="671a5-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="671a5-191">*Appartenenza<br>(dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="671a5-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="671a5-192">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-192">**Field Name**</span></span>           |<span data-ttu-id="671a5-193">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-193">**Type**</span></span>  |<span data-ttu-id="671a5-194">**Nome campo**</span><span class="sxs-lookup"><span data-stu-id="671a5-194">**Field Name**</span></span>|<span data-ttu-id="671a5-195">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="671a5-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="671a5-196">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-196">string</span></span>    |`RoleId`      |<span data-ttu-id="671a5-197">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="671a5-198">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-198">string</span></span>    |`UserId`      |<span data-ttu-id="671a5-199">stringa</span><span class="sxs-lookup"><span data-stu-id="671a5-199">string</span></span>                     |

<span data-ttu-id="671a5-200">Quando si crea uno script di migrazione per *utenti* e *ruoli*, fare riferimento alle tabelle di mapping precedenti.</span><span class="sxs-lookup"><span data-stu-id="671a5-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="671a5-201">Nell'esempio seguente si presuppone che si disponga di due database in un server di database.</span><span class="sxs-lookup"><span data-stu-id="671a5-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="671a5-202">Un database contiene i dati e lo schema di appartenenza ASP.NET esistente.</span><span class="sxs-lookup"><span data-stu-id="671a5-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="671a5-203">L'altro database *CoreIdentitySample* è stato creato usando i passaggi descritti in precedenza.</span><span class="sxs-lookup"><span data-stu-id="671a5-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="671a5-204">Per ulteriori informazioni, sono inclusi i commenti inline.</span><span class="sxs-lookup"><span data-stu-id="671a5-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="671a5-205">Al termine dello script precedente, l'app ASP.NET Core Identity creata in precedenza viene popolata con gli utenti di appartenenza.</span><span class="sxs-lookup"><span data-stu-id="671a5-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="671a5-206">Gli utenti devono modificare le password prima di eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="671a5-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="671a5-207">Se il sistema di appartenenze avesse utenti con nomi utente che non corrispondono all'indirizzo di posta elettronica, le modifiche sono necessarie per l'app creata in precedenza per soddisfare questo problema.</span><span class="sxs-lookup"><span data-stu-id="671a5-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="671a5-208">Il modello predefinito prevede che `UserName` e `Email` siano uguali.</span><span class="sxs-lookup"><span data-stu-id="671a5-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="671a5-209">Per le situazioni in cui sono diverse, è necessario modificare il processo di accesso per usare `UserName` anziché `Email`.</span><span class="sxs-lookup"><span data-stu-id="671a5-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="671a5-210">Nella pagina `PageModel` di accesso di, che si trova in *Pages\Account\Login.cshtml.cs*, rimuovere `[EmailAddress]` l'attributo dalla proprietà *email* .</span><span class="sxs-lookup"><span data-stu-id="671a5-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="671a5-211">Rinominare il *nome utente*.</span><span class="sxs-lookup"><span data-stu-id="671a5-211">Rename it to *UserName*.</span></span> <span data-ttu-id="671a5-212">Questa operazione richiede una modifica `EmailAddress` ovunque sia indicato, nella *vista* e in *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="671a5-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="671a5-213">Il risultato è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="671a5-213">The result looks like the following:</span></span>

 ![Accesso fisso](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="671a5-215">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="671a5-215">Next steps</span></span>

<span data-ttu-id="671a5-216">In questa esercitazione si è appreso come trasferire gli utenti dall'appartenenza SQL a ASP.NET Core Identity2,0.</span><span class="sxs-lookup"><span data-stu-id="671a5-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="671a5-217">Per ulteriori informazioni sulla ASP.NET Core Identity, vedere [Introduzione a Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="671a5-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
