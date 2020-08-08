---
title: Identitypersonalizzazione del modello in ASP.NET Core
author: ajcvickers
description: Questo articolo descrive come personalizzare il modello di dati Entity Framework Core sottostante per ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 4e6d91de013755f1ae998e36481f4c3b659270ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022004"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="5f5b8-103">Identitypersonalizzazione del modello in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f5b8-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="5f5b8-104">Di [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="5f5b8-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="5f5b8-105">ASP.NET Core Identity fornisce un Framework per la gestione e l'archiviazione degli account utente nelle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="5f5b8-106">Identityviene aggiunto al progetto quando si selezionano **singoli account utente** come meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="5f5b8-107">Per impostazione predefinita, Identity Usa un modello di dati di base Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="5f5b8-108">Questo articolo descrive come personalizzare il Identity modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-108">This article describes how to customize the Identity model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="5f5b8-109">Identitye EF Core migrazioni</span><span class="sxs-lookup"><span data-stu-id="5f5b8-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="5f5b8-110">Prima di esaminare il modello, è utile comprendere come Identity funziona con [EF Core migrazioni](/ef/core/managing-schemas/migrations/) per creare e aggiornare un database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="5f5b8-111">Al livello principale, il processo è:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="5f5b8-112">Definire o aggiornare un [modello di dati nel codice](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="5f5b8-113">Aggiungere una migrazione per tradurre questo modello in modifiche che possono essere applicate al database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="5f5b8-114">Verificare che la migrazione rappresenti correttamente le proprie intenzioni.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="5f5b8-115">Applicare la migrazione per aggiornare il database in modo che sia sincronizzato con il modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="5f5b8-116">Ripetere i passaggi da 1 a 4 per perfezionare ulteriormente il modello e lasciare sincronizzato il database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="5f5b8-117">Per aggiungere e applicare migrazioni, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="5f5b8-118">La finestra **console di gestione pacchetti** (PMC) se si usa Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="5f5b8-119">Per ulteriori informazioni, vedere [EF Core strumenti di PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="5f5b8-120">Interfaccia della riga di comando di .NET Core se si utilizza la riga di comando.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="5f5b8-121">Per ulteriori informazioni, vedere [EF Core strumenti della riga di comando .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="5f5b8-122">Quando si esegue l'app, fare clic sul pulsante **applica migrazioni** della pagina di errore.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="5f5b8-123">ASP.NET Core dispone di un gestore di pagine di errore in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="5f5b8-124">Il gestore può applicare le migrazioni quando viene eseguita l'app.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="5f5b8-125">Le app di produzione generano in genere script SQL dalle migrazioni e distribuiscono le modifiche al database come parte di una distribuzione controllata di app e database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="5f5b8-126">Quando viene creata una nuova app usando Identity , i passaggi 1 e 2 precedenti sono già stati completati.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="5f5b8-127">Ovvero il modello di dati iniziale esiste già e la migrazione iniziale è stata aggiunta al progetto.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="5f5b8-128">È ancora necessario applicare la migrazione iniziale al database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="5f5b8-129">La migrazione iniziale può essere applicata tramite uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="5f5b8-130">Eseguire `Update-Database` in PMC.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="5f5b8-131">Eseguire `dotnet ef database update` in una shell comandi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="5f5b8-132">Quando si esegue l'app, fare clic sul pulsante **applica migrazioni** della pagina di errore.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="5f5b8-133">Ripetere i passaggi precedenti quando vengono apportate modifiche al modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="5f5b8-134">IdentityModello</span><span class="sxs-lookup"><span data-stu-id="5f5b8-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="5f5b8-135">Tipi di entità</span><span class="sxs-lookup"><span data-stu-id="5f5b8-135">Entity types</span></span>

<span data-ttu-id="5f5b8-136">Il Identity modello è costituito dai tipi di entità seguenti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="5f5b8-137">Tipo di entità</span><span class="sxs-lookup"><span data-stu-id="5f5b8-137">Entity type</span></span>|<span data-ttu-id="5f5b8-138">Descrizione</span><span class="sxs-lookup"><span data-stu-id="5f5b8-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="5f5b8-139">Rappresenta l'utente.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="5f5b8-140">Rappresenta un ruolo.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="5f5b8-141">Rappresenta un'attestazione posseduta da un utente.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="5f5b8-142">Rappresenta un token di autenticazione per un utente.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="5f5b8-143">Associa un utente a un account di accesso.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="5f5b8-144">Rappresenta un'attestazione concessa a tutti gli utenti all'interno di un ruolo.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="5f5b8-145">Entità di join che associa utenti e ruoli.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="5f5b8-146">Relazioni tra tipi di entità</span><span class="sxs-lookup"><span data-stu-id="5f5b8-146">Entity type relationships</span></span>

<span data-ttu-id="5f5b8-147">I [tipi di entità](#entity-types) sono correlati tra loro nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="5f5b8-148">Ognuno `User` può avere molti `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="5f5b8-149">Ognuno `User` può avere molti `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="5f5b8-150">Ognuno `User` può avere molti `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="5f5b8-151">Ognuno `Role` può avere molti associati `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="5f5b8-152">Ognuno `User` può avere molti associati `Roles` , ognuno dei quali `Role` può essere associato a molti `Users` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="5f5b8-153">Si tratta di una relazione molti-a-molti che richiede una tabella di join nel database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="5f5b8-154">La tabella di join è rappresentata dall' `UserRole` entità.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="5f5b8-155">Configurazione del modello predefinito</span><span class="sxs-lookup"><span data-stu-id="5f5b8-155">Default model configuration</span></span>

<span data-ttu-id="5f5b8-156">Identitydefinisce molte *classi di contesto* che ereditano da [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) per configurare e utilizzare il modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="5f5b8-157">Questa configurazione viene eseguita usando l' [API EF Core Code First Fluent](/ef/core/modeling/) nel metodo [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) della classe Context.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="5f5b8-158">La configurazione predefinita è:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="5f5b8-159">Tipi generici di modelli</span><span class="sxs-lookup"><span data-stu-id="5f5b8-159">Model generic types</span></span>

<span data-ttu-id="5f5b8-160">Identitydefinisce i tipi CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) predefiniti per ognuno dei tipi di entità elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="5f5b8-161">Questi tipi hanno tutti il prefisso *Identity* :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="5f5b8-162">Anziché usare direttamente questi tipi, i tipi possono essere usati come classi di base per i tipi di app.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="5f5b8-163">Le `DbContext` classi definite da Identity sono generiche, in modo che tipi CLR diversi possano essere utilizzati per uno o più tipi di entità nel modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="5f5b8-164">Questi tipi generici consentono anche la `User` modifica del tipo di dati della chiave primaria (PK).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="5f5b8-165">Quando Identity si usa con supporto per i ruoli, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> è necessario usare una classe.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="5f5b8-166">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="5f5b8-167">È anche possibile usare Identity senza ruoli (solo attestazioni), nel qual caso <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> deve essere usata una classe:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="5f5b8-168">Personalizzare il modello</span><span class="sxs-lookup"><span data-stu-id="5f5b8-168">Customize the model</span></span>

<span data-ttu-id="5f5b8-169">Il punto di partenza per la personalizzazione del modello consiste nel derivare dal tipo di contesto appropriato.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="5f5b8-170">Vedere la sezione relativa ai [tipi generici del modello](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="5f5b8-171">Questo tipo di contesto viene chiamato abitualmente `ApplicationDbContext` e viene creato dai modelli di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="5f5b8-172">Il contesto viene utilizzato per configurare il modello in due modi:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="5f5b8-173">Fornire tipi di entità e di chiave per i parametri di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="5f5b8-174">Override `OnModelCreating` per modificare il mapping di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="5f5b8-175">Quando si esegue l'override `OnModelCreating` di, `base.OnModelCreating` deve essere chiamato per primo; la configurazione che esegue l'override deve essere chiamata avanti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="5f5b8-176">Per la configurazione EF Core in genere sono presenti criteri per la configurazione.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="5f5b8-177">Se, ad esempio, il `ToTable` metodo per un tipo di entità viene chiamato prima con un nome di tabella e successivamente con un nome di tabella diverso, viene usato il nome della tabella nella seconda chiamata.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="5f5b8-178">Dati utente personalizzati</span><span class="sxs-lookup"><span data-stu-id="5f5b8-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="5f5b8-179">[I dati utente personalizzati](xref:security/authentication/add-user-data) sono supportati ereditando da `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="5f5b8-180">È personalizzato per assegnare un nome a questo tipo `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="5f5b8-181">Usare il `ApplicationUser` tipo come argomento generico per il contesto:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="5f5b8-182">Non è necessario eseguire l'override `OnModelCreating` nella `ApplicationDbContext` classe.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="5f5b8-183">EF Core esegue il mapping della `CustomTag` proprietà per convenzione.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="5f5b8-184">Tuttavia, per creare una nuova colonna è necessario aggiornare il database `CustomTag` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="5f5b8-185">Per creare la colonna, aggiungere una migrazione, quindi aggiornare il database come descritto in [ Identity e EF Core migrazioni](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="5f5b8-186">Aggiornare *pages/Shared/_LoginPartial. cshtml* e sostituire `IdentityUser` con `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="5f5b8-187">Aggiornare le *aree/ Identity / Identity HostingStartup.cs* o `Startup.ConfigureServices` e sostituire `IdentityUser` con `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="5f5b8-188">In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="5f5b8-189">Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5f5b8-190">Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5f5b8-191">Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="5f5b8-192">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-192">For more information, see:</span></span>

* [<span data-ttu-id="5f5b8-193">ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="5f5b8-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="5f5b8-194">Aggiungere, scaricare ed eliminare dati utente personalizzatiIdentity</span><span class="sxs-lookup"><span data-stu-id="5f5b8-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="5f5b8-195">Modificare il tipo di chiave primaria</span><span class="sxs-lookup"><span data-stu-id="5f5b8-195">Change the primary key type</span></span>

<span data-ttu-id="5f5b8-196">Una modifica al tipo di dati della colonna PK dopo che è stato creato il database è problematica in molti sistemi di database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="5f5b8-197">La modifica del PK in genere comporta l'eliminazione e la ricreazione della tabella.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="5f5b8-198">I tipi di chiave devono pertanto essere specificati durante la migrazione iniziale al momento della creazione del database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="5f5b8-199">Per modificare il tipo di PK, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="5f5b8-200">Se il database è stato creato prima della modifica del PK, eseguire `Drop-Database` (PMC) o `dotnet ef database drop` (interfaccia della riga di comando di .NET Core) per eliminarlo.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="5f5b8-201">Dopo la conferma dell'eliminazione del database, rimuovere la migrazione iniziale con `Remove-Migration` (PMC) o `dotnet ef migrations remove` (interfaccia della riga di comando di .NET Core).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="5f5b8-202">Aggiornare la `ApplicationDbContext` classe da cui derivare <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="5f5b8-203">Specificare il nuovo tipo di chiave per `TKey` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="5f5b8-204">Ad esempio, per usare un `Guid` tipo di chiave:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="5f5b8-205">Nel codice precedente, le classi generiche <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> devono essere specificate per usare il nuovo tipo di chiave.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="5f5b8-206">Nel codice precedente, le classi generiche <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> devono essere specificate per usare il nuovo tipo di chiave.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="5f5b8-207">`Startup.ConfigureServices`è necessario aggiornare per usare l'utente generico:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="5f5b8-208">Se `ApplicationUser` viene utilizzata una classe personalizzata, aggiornare la classe da cui ereditare `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="5f5b8-209">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="5f5b8-210">Aggiornare `ApplicationDbContext` per fare riferimento alla `ApplicationUser` classe personalizzata:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="5f5b8-211">Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5f5b8-212">Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="5f5b8-213">In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="5f5b8-214">Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5f5b8-215">Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5f5b8-216">Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5f5b8-217">Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5f5b8-218">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> metodo accetta un `TKey` tipo che indica il tipo di dati della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="5f5b8-219">Se `ApplicationRole` viene utilizzata una classe personalizzata, aggiornare la classe da cui ereditare `IdentityRole<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="5f5b8-220">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="5f5b8-221">Aggiornare `ApplicationDbContext` per fare riferimento alla `ApplicationRole` classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="5f5b8-222">La classe seguente, ad esempio, fa riferimento a un oggetto personalizzato `ApplicationUser` e personalizzato `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5f5b8-223">Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="5f5b8-224">Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="5f5b8-225">In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="5f5b8-226">Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5f5b8-227">Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5f5b8-228">Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5f5b8-229">Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="5f5b8-230">Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5f5b8-231">Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="5f5b8-232">Il <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> metodo accetta un `TKey` tipo che indica il tipo di dati della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="5f5b8-233">Aggiungi proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="5f5b8-233">Add navigation properties</span></span>

<span data-ttu-id="5f5b8-234">La modifica della configurazione del modello per le relazioni può risultare più difficile rispetto a apportare altre modifiche.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="5f5b8-235">È necessario prestare attenzione a sostituire le relazioni esistenti anziché creare nuove relazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="5f5b8-236">In particolare, è necessario che la relazione modificata specifichi la stessa proprietà di chiave esterna (FK) della relazione esistente.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="5f5b8-237">Ad esempio, la relazione tra `Users` e `UserClaims` è, per impostazione predefinita, specificata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="5f5b8-238">L'FK per questa relazione viene specificato come `UserClaim.UserId` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="5f5b8-239">`HasMany`e `WithOne` vengono chiamati senza argomenti per creare la relazione senza proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="5f5b8-240">Aggiungere una proprietà di navigazione a `ApplicationUser` che consente di fare riferimento all'oggetto associato `UserClaims` all'utente:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="5f5b8-241">`TKey`Per `IdentityUserClaim<TKey>` è il tipo specificato per il PK di utenti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="5f5b8-242">In questo caso, `TKey` è `string` perché vengono usate le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="5f5b8-243">**Non** si tratta del tipo PK per il `UserClaim` tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="5f5b8-244">Ora che la proprietà di navigazione esiste, è necessario configurarla in `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="5f5b8-245">Si noti che la relazione è configurata esattamente come in precedenza, ma solo con una proprietà di navigazione specificata nella chiamata a `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="5f5b8-246">Le proprietà di navigazione sono disponibili solo nel modello EF e non nel database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="5f5b8-247">Poiché l'FK per la relazione non è stato modificato, questo tipo di modifica del modello non richiede l'aggiornamento del database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="5f5b8-248">Questa operazione può essere controllata aggiungendo una migrazione dopo aver apportato la modifica.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="5f5b8-249">I `Up` `Down` metodi e sono vuoti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="5f5b8-250">Aggiungi tutte le proprietà di navigazione utente</span><span class="sxs-lookup"><span data-stu-id="5f5b8-250">Add all User navigation properties</span></span>

<span data-ttu-id="5f5b8-251">Utilizzando la sezione precedente come materiale sussidiario, nell'esempio seguente vengono configurate le proprietà di navigazione unidirezionale per tutte le relazioni nell'utente:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="5f5b8-252">Aggiungere le proprietà di navigazione utente e ruolo</span><span class="sxs-lookup"><span data-stu-id="5f5b8-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="5f5b8-253">Utilizzando la sezione precedente come materiale sussidiario, nell'esempio seguente vengono configurate le proprietà di navigazione per tutte le relazioni su utente e ruolo:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="5f5b8-254">Note:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-254">Notes:</span></span>

* <span data-ttu-id="5f5b8-255">Questo esempio include anche l' `UserRole` entità join, necessaria per spostarsi tra gli utenti e i ruoli della relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="5f5b8-256">Ricordarsi di modificare i tipi delle proprietà di navigazione in modo da riflettere che i `ApplicationXxx` tipi vengono ora usati al posto dei `IdentityXxx` tipi.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="5f5b8-257">Ricordarsi di usare `ApplicationXxx` nella definizione generica `ApplicationContext` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="5f5b8-258">Aggiungi tutte le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="5f5b8-258">Add all navigation properties</span></span>

<span data-ttu-id="5f5b8-259">Utilizzando la sezione precedente come guida, nell'esempio seguente vengono configurate le proprietà di navigazione per tutte le relazioni in tutti i tipi di entità:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="5f5b8-260">USA chiavi composite</span><span class="sxs-lookup"><span data-stu-id="5f5b8-260">Use composite keys</span></span>

<span data-ttu-id="5f5b8-261">Nelle sezioni precedenti è stata illustrata la modifica del tipo di chiave utilizzato nel Identity modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="5f5b8-262">La modifica del Identity modello di chiave per l'utilizzo di chiavi composite non è supportata o consigliata.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="5f5b8-263">L'uso di una chiave composta con Identity comporta la modifica del modo Identity in cui il codice del gestore interagisce con il modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="5f5b8-264">Questa personalizzazione esula dall'ambito di questo documento.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="5f5b8-265">Modificare i nomi di tabella/colonna e i facet</span><span class="sxs-lookup"><span data-stu-id="5f5b8-265">Change table/column names and facets</span></span>

<span data-ttu-id="5f5b8-266">Per modificare i nomi di tabelle e colonne, chiamare `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="5f5b8-267">Quindi, aggiungere la configurazione per sostituire le impostazioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="5f5b8-268">Ad esempio, per modificare il nome di tutte le Identity tabelle:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="5f5b8-269">In questi esempi vengono usati i Identity tipi predefiniti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-269">These examples use the default Identity types.</span></span> <span data-ttu-id="5f5b8-270">Se si usa un tipo di app come `ApplicationUser` , configurare quel tipo anziché il tipo predefinito.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="5f5b8-271">Nell'esempio seguente vengono modificati alcuni nomi di colonna:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="5f5b8-272">Alcuni tipi di colonne di database possono essere configurati con determinati *facet* , ad esempio la `string` lunghezza massima consentita.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="5f5b8-273">Nell'esempio seguente vengono impostate le lunghezze massime delle colonne per diverse `string` proprietà nel modello:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="5f5b8-274">Eseguire il mapping a uno schema diverso</span><span class="sxs-lookup"><span data-stu-id="5f5b8-274">Map to a different schema</span></span>

<span data-ttu-id="5f5b8-275">Gli schemi possono comportarsi in modo diverso tra i provider di database.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="5f5b8-276">Per SQL Server, l'impostazione predefinita consiste nel creare tutte le tabelle nello schema *dbo* .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="5f5b8-277">È possibile creare le tabelle in uno schema diverso.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="5f5b8-278">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="5f5b8-279">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="5f5b8-279">Lazy loading</span></span>

<span data-ttu-id="5f5b8-280">In questa sezione viene aggiunto il supporto per i proxy di caricamento lazy nel Identity modello.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="5f5b8-281">Il caricamento lazy è utile perché consente di utilizzare le proprietà di navigazione senza prima verificare che siano caricate.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="5f5b8-282">I tipi di entità possono essere resi idonei per il caricamento lazy in diversi modi, come descritto nella [documentazione EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5f5b8-283">Per semplicità, usare proxy di caricamento lazy, che richiedono:</span><span class="sxs-lookup"><span data-stu-id="5f5b8-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="5f5b8-284">Installazione del pacchetto [Microsoft. EntityFrameworkCore. Proxys](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="5f5b8-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="5f5b8-285">Una chiamata a <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> all'interno di [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="5f5b8-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="5f5b8-286">Tipi di entità pubblici con `public virtual` proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="5f5b8-287">Nell'esempio seguente viene illustrata la chiamata `UseLazyLoadingProxies` a in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5f5b8-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="5f5b8-288">Per istruzioni sull'aggiunta di proprietà di navigazione ai tipi di entità, vedere gli esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="5f5b8-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f5b8-289">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5f5b8-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
