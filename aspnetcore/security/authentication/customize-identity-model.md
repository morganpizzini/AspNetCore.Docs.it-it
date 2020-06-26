---
title: Identitypersonalizzazione del modello in ASP.NET Core
author: ajcvickers
description: Questo articolo descrive come personalizzare il modello di dati Entity Framework Core sottostante per ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 3a5bac0e3e34602b1f8a85a7bcde1ba92b372607
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399166"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Identitypersonalizzazione del modello in ASP.NET Core

Di [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity fornisce un Framework per la gestione e l'archiviazione degli account utente nelle app ASP.NET Core. Identityviene aggiunto al progetto quando si selezionano **singoli account utente** come meccanismo di autenticazione. Per impostazione predefinita, Identity Usa un modello di dati di base Entity Framework (EF). Questo articolo descrive come personalizzare il Identity modello.

## <a name="identity-and-ef-core-migrations"></a>Identitye EF Core migrazioni

Prima di esaminare il modello, è utile comprendere come Identity funziona con [EF Core migrazioni](/ef/core/managing-schemas/migrations/) per creare e aggiornare un database. Al livello principale, il processo è:

1. Definire o aggiornare un [modello di dati nel codice](/ef/core/modeling/).
1. Aggiungere una migrazione per tradurre questo modello in modifiche che possono essere applicate al database.
1. Verificare che la migrazione rappresenti correttamente le proprie intenzioni.
1. Applicare la migrazione per aggiornare il database in modo che sia sincronizzato con il modello.
1. Ripetere i passaggi da 1 a 4 per perfezionare ulteriormente il modello e lasciare sincronizzato il database.

Per aggiungere e applicare migrazioni, usare uno degli approcci seguenti:

* La finestra **console di gestione pacchetti** (PMC) se si usa Visual Studio. Per ulteriori informazioni, vedere [EF Core strumenti di PMC](/ef/core/miscellaneous/cli/powershell).
* Interfaccia della riga di comando di .NET Core se si utilizza la riga di comando. Per ulteriori informazioni, vedere [EF Core strumenti della riga di comando .NET](/ef/core/miscellaneous/cli/dotnet).
* Quando si esegue l'app, fare clic sul pulsante **applica migrazioni** della pagina di errore.

ASP.NET Core dispone di un gestore di pagine di errore in fase di sviluppo. Il gestore può applicare le migrazioni quando viene eseguita l'app. Le app di produzione generano in genere script SQL dalle migrazioni e distribuiscono le modifiche al database come parte di una distribuzione controllata di app e database.

Quando viene creata una nuova app usando Identity , i passaggi 1 e 2 precedenti sono già stati completati. Ovvero il modello di dati iniziale esiste già e la migrazione iniziale è stata aggiunta al progetto. È ancora necessario applicare la migrazione iniziale al database. La migrazione iniziale può essere applicata tramite uno degli approcci seguenti:

* Eseguire `Update-Database` in PMC.
* Eseguire `dotnet ef database update` in una shell comandi.
* Quando si esegue l'app, fare clic sul pulsante **applica migrazioni** della pagina di errore.

Ripetere i passaggi precedenti quando vengono apportate modifiche al modello.

## <a name="the-identity-model"></a>IdentityModello

### <a name="entity-types"></a>Tipi di entità

Il Identity modello è costituito dai tipi di entità seguenti.

|Tipo di entità|Descrizione                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Rappresenta l'utente.                                         |
|`Role`     |Rappresenta un ruolo.                                           |
|`UserClaim`|Rappresenta un'attestazione posseduta da un utente.                    |
|`UserToken`|Rappresenta un token di autenticazione per un utente.               |
|`UserLogin`|Associa un utente a un account di accesso.                              |
|`RoleClaim`|Rappresenta un'attestazione concessa a tutti gli utenti all'interno di un ruolo.|
|`UserRole` |Entità di join che associa utenti e ruoli.               |

### <a name="entity-type-relationships"></a>Relazioni tra tipi di entità

I [tipi di entità](#entity-types) sono correlati tra loro nei modi seguenti:

* Ognuno `User` può avere molti `UserClaims` .
* Ognuno `User` può avere molti `UserLogins` .
* Ognuno `User` può avere molti `UserTokens` .
* Ognuno `Role` può avere molti associati `RoleClaims` .
* Ognuno `User` può avere molti associati `Roles` , ognuno dei quali `Role` può essere associato a molti `Users` . Si tratta di una relazione molti-a-molti che richiede una tabella di join nel database. La tabella di join è rappresentata dall' `UserRole` entità.

### <a name="default-model-configuration"></a>Configurazione del modello predefinito

Identitydefinisce molte *classi di contesto* che ereditano da [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) per configurare e utilizzare il modello. Questa configurazione viene eseguita usando l' [API EF Core Code First Fluent](/ef/core/modeling/) nel metodo [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) della classe Context. La configurazione predefinita è:

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

### <a name="model-generic-types"></a>Tipi generici di modelli

Identitydefinisce i tipi CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) predefiniti per ognuno dei tipi di entità elencati in precedenza. Questi tipi hanno tutti il prefisso *Identity* :

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Anziché usare direttamente questi tipi, i tipi possono essere usati come classi di base per i tipi di app. Le `DbContext` classi definite da Identity sono generiche, in modo che tipi CLR diversi possano essere utilizzati per uno o più tipi di entità nel modello. Questi tipi generici consentono anche la `User` modifica del tipo di dati della chiave primaria (PK).

Quando Identity si usa con supporto per i ruoli, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> è necessario usare una classe. Ad esempio:

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

È anche possibile usare Identity senza ruoli (solo attestazioni), nel qual caso <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> deve essere usata una classe:

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

## <a name="customize-the-model"></a>Personalizzare il modello

Il punto di partenza per la personalizzazione del modello consiste nel derivare dal tipo di contesto appropriato. Vedere la sezione relativa ai [tipi generici del modello](#model-generic-types) . Questo tipo di contesto viene chiamato abitualmente `ApplicationDbContext` e viene creato dai modelli di ASP.NET Core.

Il contesto viene utilizzato per configurare il modello in due modi:

* Fornire tipi di entità e di chiave per i parametri di tipo generico.
* Override `OnModelCreating` per modificare il mapping di questi tipi.

Quando si esegue l'override `OnModelCreating` di, `base.OnModelCreating` deve essere chiamato per primo; la configurazione che esegue l'override deve essere chiamata avanti. Per la configurazione EF Core in genere sono presenti criteri per la configurazione. Se, ad esempio, il `ToTable` metodo per un tipo di entità viene chiamato prima con un nome di tabella e successivamente con un nome di tabella diverso, viene usato il nome della tabella nella seconda chiamata.

### <a name="custom-user-data"></a>Dati utente personalizzati

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[I dati utente personalizzati](xref:security/authentication/add-user-data) sono supportati ereditando da `IdentityUser` . È personalizzato per assegnare un nome a questo tipo `ApplicationUser` :

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Usare il `ApplicationUser` tipo come argomento generico per il contesto:

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

Non è necessario eseguire l'override `OnModelCreating` nella `ApplicationDbContext` classe. EF Core esegue il mapping della `CustomTag` proprietà per convenzione. Tuttavia, per creare una nuova colonna è necessario aggiornare il database `CustomTag` . Per creare la colonna, aggiungere una migrazione, quindi aggiornare il database come descritto in [ Identity e EF Core migrazioni](#identity-and-ef-core-migrations).

Aggiornare *pages/Shared/_LoginPartial. cshtml* e sostituire `IdentityUser` con `ApplicationUser` :

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Aggiornare le *aree/ Identity /IdentityHostingStartup.cs* o `Startup.ConfigureServices` e sostituire `IdentityUser` con `ApplicationUser` .

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi. Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>. Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` . Per altre informazioni, vedere:

* [ScaffoldIdentity](xref:security/authentication/scaffold-identity)
* [Aggiungere, scaricare ed eliminare dati utente personalizzatiIdentity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Modificare il tipo di chiave primaria

Una modifica al tipo di dati della colonna PK dopo che è stato creato il database è problematica in molti sistemi di database. La modifica del PK in genere comporta l'eliminazione e la ricreazione della tabella. I tipi di chiave devono pertanto essere specificati durante la migrazione iniziale al momento della creazione del database.

Per modificare il tipo di PK, attenersi alla procedura seguente:

1. Se il database è stato creato prima della modifica del PK, eseguire `Drop-Database` (PMC) o `dotnet ef database drop` (interfaccia della riga di comando di .NET Core) per eliminarlo.
2. Dopo la conferma dell'eliminazione del database, rimuovere la migrazione iniziale con `Remove-Migration` (PMC) o `dotnet ef migrations remove` (interfaccia della riga di comando di .NET Core).
3. Aggiornare la `ApplicationDbContext` classe da cui derivare <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> . Specificare il nuovo tipo di chiave per `TKey` . Ad esempio, per usare un `Guid` tipo di chiave:

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

    Nel codice precedente, le classi generiche <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> devono essere specificate per usare il nuovo tipo di chiave.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    Nel codice precedente, le classi generiche <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> e <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> devono essere specificate per usare il nuovo tipo di chiave.

    ::: moniker-end

    `Startup.ConfigureServices`è necessario aggiornare per usare l'utente generico:

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

4. Se `ApplicationUser` viene utilizzata una classe personalizzata, aggiornare la classe da cui ereditare `IdentityUser` . Ad esempio:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Aggiornare `ApplicationDbContext` per fare riferimento alla `ApplicationUser` classe personalizzata:

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

    Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi. Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>. Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    Il <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> metodo accetta un `TKey` tipo che indica il tipo di dati della chiave primaria.

    ::: moniker-end

5. Se `ApplicationRole` viene utilizzata una classe personalizzata, aggiornare la classe da cui ereditare `IdentityRole<TKey>` . Ad esempio:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Aggiornare `ApplicationDbContext` per fare riferimento alla `ApplicationRole` classe personalizzata. La classe seguente, ad esempio, fa riferimento a un oggetto personalizzato `ApplicationUser` e personalizzato `ApplicationRole` :

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    In ASP.NET Core 2,1 o versioni successive Identity viene fornito come Razor libreria di classi. Per altre informazioni, vedere <xref:security/authentication/scaffold-identity>. Di conseguenza, il codice precedente richiede una chiamata a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Se l' Identity impalcatura è stata usata per aggiungere Identity file al progetto, rimuovere la chiamata a `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Il tipo di dati della chiave primaria viene dedotto analizzando l'oggetto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrare la classe del contesto di database personalizzata quando si aggiunge il Identity servizio in `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Il <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> metodo accetta un `TKey` tipo che indica il tipo di dati della chiave primaria.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Aggiungi proprietà di navigazione

La modifica della configurazione del modello per le relazioni può risultare più difficile rispetto a apportare altre modifiche. È necessario prestare attenzione a sostituire le relazioni esistenti anziché creare nuove relazioni aggiuntive. In particolare, è necessario che la relazione modificata specifichi la stessa proprietà di chiave esterna (FK) della relazione esistente. Ad esempio, la relazione tra `Users` e `UserClaims` è, per impostazione predefinita, specificata come indicato di seguito:

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

L'FK per questa relazione viene specificato come `UserClaim.UserId` Proprietà. `HasMany`e `WithOne` vengono chiamati senza argomenti per creare la relazione senza proprietà di navigazione.

Aggiungere una proprietà di navigazione a `ApplicationUser` che consente di fare riferimento all'oggetto associato `UserClaims` all'utente:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey`Per `IdentityUserClaim<TKey>` è il tipo specificato per il PK di utenti. In questo caso, `TKey` è `string` perché vengono usate le impostazioni predefinite. **Non** si tratta del tipo PK per il `UserClaim` tipo di entità.

Ora che la proprietà di navigazione esiste, è necessario configurarla in `OnModelCreating` :

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

Si noti che la relazione è configurata esattamente come in precedenza, ma solo con una proprietà di navigazione specificata nella chiamata a `HasMany` .

Le proprietà di navigazione sono disponibili solo nel modello EF e non nel database. Poiché l'FK per la relazione non è stato modificato, questo tipo di modifica del modello non richiede l'aggiornamento del database. Questa operazione può essere controllata aggiungendo una migrazione dopo aver apportato la modifica. I `Up` `Down` metodi e sono vuoti.

### <a name="add-all-user-navigation-properties"></a>Aggiungi tutte le proprietà di navigazione utente

Utilizzando la sezione precedente come materiale sussidiario, nell'esempio seguente vengono configurate le proprietà di navigazione unidirezionale per tutte le relazioni nell'utente:

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

### <a name="add-user-and-role-navigation-properties"></a>Aggiungere le proprietà di navigazione utente e ruolo

Utilizzando la sezione precedente come materiale sussidiario, nell'esempio seguente vengono configurate le proprietà di navigazione per tutte le relazioni su utente e ruolo:

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

Note:

* Questo esempio include anche l' `UserRole` entità join, necessaria per spostarsi tra gli utenti e i ruoli della relazione molti-a-molti.
* Ricordarsi di modificare i tipi delle proprietà di navigazione in modo da riflettere che i `ApplicationXxx` tipi vengono ora usati al posto dei `IdentityXxx` tipi.
* Ricordarsi di usare `ApplicationXxx` nella definizione generica `ApplicationContext` .

### <a name="add-all-navigation-properties"></a>Aggiungi tutte le proprietà di navigazione

Utilizzando la sezione precedente come guida, nell'esempio seguente vengono configurate le proprietà di navigazione per tutte le relazioni in tutti i tipi di entità:

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

### <a name="use-composite-keys"></a>USA chiavi composite

Nelle sezioni precedenti è stata illustrata la modifica del tipo di chiave utilizzato nel Identity modello. La modifica del Identity modello di chiave per l'utilizzo di chiavi composite non è supportata o consigliata. L'uso di una chiave composta con Identity comporta la modifica del modo Identity in cui il codice del gestore interagisce con il modello. Questa personalizzazione esula dall'ambito di questo documento.

### <a name="change-tablecolumn-names-and-facets"></a>Modificare i nomi di tabella/colonna e i facet

Per modificare i nomi di tabelle e colonne, chiamare `base.OnModelCreating` . Quindi, aggiungere la configurazione per sostituire le impostazioni predefinite. Ad esempio, per modificare il nome di tutte le Identity tabelle:

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

In questi esempi vengono usati i Identity tipi predefiniti. Se si usa un tipo di app come `ApplicationUser` , configurare quel tipo anziché il tipo predefinito.

Nell'esempio seguente vengono modificati alcuni nomi di colonna:

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

Alcuni tipi di colonne di database possono essere configurati con determinati *facet* , ad esempio la `string` lunghezza massima consentita. Nell'esempio seguente vengono impostate le lunghezze massime delle colonne per diverse `string` proprietà nel modello:

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

### <a name="map-to-a-different-schema"></a>Eseguire il mapping a uno schema diverso

Gli schemi possono comportarsi in modo diverso tra i provider di database. Per SQL Server, l'impostazione predefinita consiste nel creare tutte le tabelle nello schema *dbo* . È possibile creare le tabelle in uno schema diverso. Ad esempio:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Caricamento lazy

In questa sezione viene aggiunto il supporto per i proxy di caricamento lazy nel Identity modello. Il caricamento lazy è utile perché consente di utilizzare le proprietà di navigazione senza prima verificare che siano caricate.

I tipi di entità possono essere resi idonei per il caricamento lazy in diversi modi, come descritto nella [documentazione EF Core](/ef/core/querying/related-data#lazy-loading). Per semplicità, usare proxy di caricamento lazy, che richiedono:

* Installazione del pacchetto [Microsoft. EntityFrameworkCore. Proxys](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Una chiamata a <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> all'interno di [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Tipi di entità pubblici con `public virtual` proprietà di navigazione.

Nell'esempio seguente viene illustrata la chiamata `UseLazyLoadingProxies` a in `Startup.ConfigureServices` :

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Per istruzioni sull'aggiunta di proprietà di navigazione ai tipi di entità, vedere gli esempi precedenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authentication/scaffold-identity>

::: moniker-end
